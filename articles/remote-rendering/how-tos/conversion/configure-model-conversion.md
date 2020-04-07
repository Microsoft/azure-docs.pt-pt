---
title: Configurar a conversão do modelo
description: Descrição de todos os parâmetros de conversão do modelo
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681574"
---
# <a name="configure-the-model-conversion"></a>Configurar a conversão do modelo

Este capítulo documenta as opções para a conversão do modelo.

## <a name="settings-file"></a>Ficheiro de definições

Se um `ConversionSettings.json` ficheiro chamado for encontrado no recipiente de entrada ao lado do modelo de entrada, então é utilizado para fornecer uma configuração adicional para o processo de conversão do modelo.

O conteúdo do ficheiro deve satisfazer o seguinte json schema:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Um `ConversionSettings.json` ficheiro de exemplo pode ser:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parâmetros de geometria

* `scaling`- Este parâmetro escala um modelo uniformemente. O dimensionamento pode ser usado para crescer ou encolher um modelo, por exemplo, para exibir um modelo de construção em cima de uma mesa. Uma vez que o motor de renderização espera que os comprimentos sejam especificados em metros, outra utilização importante deste parâmetro surge quando um modelo é definido em diferentes unidades. Por exemplo, se um modelo for definido em centímetros, então aplicar uma escala de 0,01 deve tornar o modelo no tamanho correto.
Alguns formatos de dados de origem (por exemplo .fbx) fornecem uma dica de escala de unidade, caso em que a conversão dimensiona implicitamente o modelo para as unidades do medidor. A escala implícita fornecida pelo formato de origem será aplicada em cima do parâmetro de escala.
O fator de escala final é aplicado aos vértices de geometria e às transformações locais dos nódos os gráficos da cena. A escala para a transformação da entidade radicular permanece inalterada.

* `recenterToOrigin`- Estabelece que um modelo deve ser convertido de modo a que a sua caixa de delimitação esteja centrada na origem.
O centro é importante se o modelo de origem for deslocado longe da origem, uma vez que, nesse caso, problemas de precisão de ponto flutuante podem causar artefactos de renderização.

* `opaqueMaterialDefaultSidedness`- O motor de renderização pressupõe que os materiais opacos são de dupla face.
Se não for esse o comportamento pretendido, este parâmetro deve ser definido para "SingleSided". Para mais informações, consulte [a renderização unilateral](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Sobreposições de material

* `material-override`- Este parâmetro permite que o processamento de materiais seja [personalizado durante](override-materials.md)a conversão .

### <a name="color-space-parameters"></a>Parâmetros do espaço de cor

O motor de renderização espera que os valores de cor estejam no espaço linear.
Se um modelo for definido usando o espaço gama, então estas opções devem ser definidas como verdadeiras.

* `gammaToLinearMaterial`- Converter cores de material do espaço gama para o espaço linear
* `gammaToLinearVertex`- Converter cores vértices do espaço gama para o espaço linear

> [!NOTE]
> Para os ficheiros FBX, estas definições são definidas `true` por padrão. Para todos os outros tipos `false`de ficheiros, a predefinição é .

### <a name="scene-parameters"></a>Parâmetros de cena

* `sceneGraphMode`- Define como o gráfico de cena no ficheiro fonte é convertido:
  * `dynamic`(predefinido): Todos os objetos do ficheiro são expostos como [entidades](../../concepts/entities.md) da API e podem ser transformados de forma independente. A hierarquia do nó em tempo de execução é idêntica à estrutura do ficheiro fonte.
  * `static`: Todos os objetos estão expostos na API, mas não podem ser transformados de forma independente.
  * `none`: O gráfico da cena é colapsado num objeto.

Cada modo tem um desempenho de tempo de execução diferente. Em `dynamic` modo, o custo de desempenho escala linearmente com o número de [entidades](../../concepts/entities.md) no gráfico, mesmo quando nenhuma parte é movida. Só deve ser utilizado quando se movem peças individualmente para a aplicação, por exemplo, para uma animação de "visão de explosão".

O `static` modo exporta o gráfico de cena completa, mas as partes dentro deste gráfico têm uma constante transformação em relação à sua parte raiz. O nó raiz do objeto, no entanto, ainda pode ser movido, rodado ou escalado sem custo suculegância de desempenho significativo. Além disso, [as consultas espaciais](../../overview/features/spatial-queries.md) devolverão peças individuais e cada peça pode ser modificada através de [sobreposições estatais](../../overview/features/override-hierarchical-state.md). Com este modo, o tempo de execução por objeto é insignificante. É ideal para grandes cenas onde ainda precisa de inspeção por objeto, mas sem alterações de transformação por objeto.

O `none` modo tem o tempo de funcionamento mínimo e também tempos de carregamento ligeiramente melhores. A inspeção ou transformação de objetos individuais não é possível neste modo. Os casos de utilização são, por exemplo, modelos de fotogrammetria que não têm um gráfico de cena significativo em primeiro lugar.

> [!TIP]
> Muitas aplicações carregarão vários modelos. Deve otimizar os parâmetros de conversão para cada modelo, dependendo da forma como será utilizado. Por exemplo, se pretender exibir o modelo de um carro para o utilizador se desmontar e inspecionar em detalhe, tem de o converter com `dynamic` modo. No entanto, se quiser colocar o carro em ambiente de sala `sceneGraphMode` de `static` espetáculos, esse modelo pode ser convertido com conjunto ou mesmo `none`.

### <a name="physics-parameters"></a>Parâmetros da física

* `generateCollisionMesh`- Se necessitar de apoio para [consultas espaciais](../../overview/features/spatial-queries.md) num modelo, esta opção tem de ser ativada. Na pior das hipóteses, a criação de uma malha de colisão pode duplicar o tempo de conversão. Os modelos com malshes de colisão demoram mais tempo a carregar e quando usam um `dynamic` gráfico de cena, também têm um desempenho mais elevado. Para um desempenho ideal geral, deve desativar esta opção em todos os modelos em que não precisa de consultas espaciais.

### <a name="unlit-materials"></a>Materiais não iluminados

* `unlitMaterials`- Por defeito, a conversão prefere criar [materiais PBR](../../overview/features/pbr-materials.md). Esta opção diz ao conversor para tratar todos os materiais como materiais de [cor.](../../overview/features/color-materials.md) Se tiver dados que já incorporam iluminação, como modelos criados através da fotogrammetria, esta opção permite-lhe aplicar rapidamente a conversão correta para todos os materiais, sem a necessidade de [sobrepor individualmente cada material.](override-materials.md)

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Convertendo-se a partir de formatos FBX mais antigos, com um modelo de material De Phong

* `fbxAssumeMetallic`- Versões mais antigas do formato FBX definem os seus materiais utilizando um modelo de material Phong. O processo de conversão tem de inferir a forma como estes materiais mapeiam para o [modelo PBR](../../overview/features/pbr-materials.md)da renderização . Normalmente isto funciona bem, mas uma ambiguidade pode surgir quando um material não tem texturas, altos valores especular, e uma cor albedo não-cinza. Nestas circunstâncias, a conversão tem de escolher entre priorizar os elevados valores especulares, definir um material metálico altamente reflexivo onde a cor do albedo se dissolve, ou priorizar a cor albedo, definindo algo como um plástico colorido brilhante. Por padrão, o processo de conversão pressupõe que valores altamente especulares implicam um material metálico nos casos em que a ambiguidade se aplica. Este parâmetro pode ser `false` definido para mudar para o oposto.

### <a name="coordinate-system-overriding"></a>Coordenar o sistema de sobrecondução

* `axis`- Para anular a coordenadagem de vetores unitários do sistema. Os valores predefinidos são `["+x", "+y", "+z"]`. Em teoria, o formato FBX tem um cabeçalho onde esses vetores são definidos e a conversão usa essa informação para transformar a cena. O formato glTF também define um sistema de coordenadas fixas. Na prática, alguns ativos têm informações incorretas no seu cabeçalho ou foram salvos com uma convenção de sistema de coordenadas diferente. Esta opção permite-lhe anular o sistema de coordenadas para compensar. Por exemplo: `"axis" : ["+x", "+z", "-y"]` trocará o eixo Z e o eixo Y e manterá a capacidade do sistema de coordenadas invertendo a direção do eixo Y.

### <a name="vertex-format"></a>Formato Vertex

É possível ajustar o formato vértice para uma malha, para trocar precisão para poupança de memória. Uma pegada de memória mais baixa permite-lhe carregar modelos maiores ou obter um melhor desempenho. No entanto, dependendo dos seus dados, o formato errado pode ter um impacto significativo na qualidade da renderização.

> [!CAUTION]
> Mudar o formato de vértice deve ser um último recurso quando os modelos já não se encaixam na memória, ou ao otimizar para o melhor desempenho possível. As mudanças podem facilmente introduzir artefactos de renderização, tanto óbvios como subtis. A menos que saiba o que procurar, não deve mudar o padrão.

Estes ajustes são possíveis:

* Os fluxos específicos de dados podem ser explicitamente incluídos ou excluídos.
* A precisão dos fluxos de dados pode ser diminuída para reduzir a pegada de memória.

A `vertex` secção seguinte `.json` no ficheiro é opcional. Para cada porção que não esteja explicitamente especificada, o serviço de conversão recai para a sua definição padrão.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Ao forçar um `NONE`componente a, é garantido que a malha de saída não tem o respetivo fluxo.

#### <a name="component-formats-per-vertex-stream"></a>Formatos de componentes por fluxo de vértice

Estes formatos são permitidos para os respetivos componentes:

| Componente vertex | Formatos suportados (arrojado = padrão) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT,** 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED,** NENHUM |
|cor1| 8_8_8_8_UNSIGNED_NORMALIZED, **NENHUM.**|
|normal| **8_8_8_8_SIGNED_NORMALIZED,** 16_16_16_16_FLOAT, NENHUM |
|tangente| **8_8_8_8_SIGNED_NORMALIZED,** 16_16_16_16_FLOAT, NENHUM |
|binormal| **8_8_8_8_SIGNED_NORMALIZED,** 16_16_16_16_FLOAT, NENHUM |
|texcoord0| **32_32_FLOAT,** 16_16_FLOAT, NENHUM |
|texcoord1| **32_32_FLOAT,** 16_16_FLOAT, NENHUM |

#### <a name="supported-component-formats"></a>Formatos de componentes suportados

As pegadas de memória dos formatos são as seguintes:

| Formato | Descrição | Bytes por vértice |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisão de ponto flutuante completo de dois componentes|8
|16_16_FLOAT|precisão de ponto flutuante de dois componentes|4
|32_32_32_FLOAT|precisão de ponto flutuante completo de três componentes|12
|16_16_16_16_FLOAT|precisão de ponto flutuante de quatro componentes|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte de quatro componentes, `[0; 1]` normalizado ao alcance|4
|8_8_8_8_SIGNED_NORMALIZED|byte de quatro componentes, `[-1; 1]` normalizado ao alcance|4

#### <a name="best-practices-for-component-format-changes"></a>Boas práticas para alterações no formato de componentes

* `position`: É raro que a precisão reduzida seja suficiente. **16_16_16_16_FLOAT** introduz artefactos de quantificação percetíveis, mesmo para pequenos modelos.
* `normal`, `tangent` `binormal`, : Normalmente estes valores são alterados em conjunto. A menos que existam artefactos de iluminação percetíveis que resultem da quantificação normal, não há razão para aumentar a sua precisão. Em alguns casos, porém, estes componentes podem ser definidos para **NENHUM:**
  * `normal`, `tangent`e `binormal` só são necessários quando pelo menos um material do modelo deve ser aceso. Em ARR, este é o caso quando um [material PBR](../../overview/features/pbr-materials.md) é usado no modelo a qualquer momento.
  * `tangent`e `binormal` só são necessários quando qualquer um dos materiais iluminados usa uma textura normal do mapa.
* `texcoord0`: `texcoord1` As coordenadas de textura podem utilizar uma precisão `[0; 1]` reduzida **(16_16_FLOAT**) quando os seus valores se mantêm na gama e quando as texturas endereçadas têm um tamanho máximo de 2048 x 2048 pixels. Se esses limites forem ultrapassados, a qualidade do mapeamento da textura sofrerá.

#### <a name="example"></a>Exemplo

Assuma que tem um modelo de fotogrammetria, que tem iluminação cozida nas texturas. Tudo o que é necessário para renderizar o modelo são posições de vértice e coordenadas de textura.

Por defeito, o conversor tem de assumir que poderá querer utilizar materiais `normal`PBR num modelo em algum momento, para que gere , `tangent`e `binormal` dados para si. Consequentemente, o uso por `position` vértice de memória `texcoord0` é (12 `normal` bytes) + `tangent` (8 bytes) `binormal` + (4 bytes) + (4 bytes) + (4 bytes) = 32 bytes. Modelos maiores deste tipo podem facilmente ter muitos milhões de vértices resultando em modelos que podem ocupar vários gigabytes de memória. Tais grandes quantidades de dados afetarão o desempenho e poderá até ficar sem memória.

Sabendo que nunca precisa de iluminação dinâmica no modelo, `[0; 1]` e sabendo que `normal` `tangent`todas `binormal` as `NONE` `texcoord0` coordenadas de`16_16_FLOAT`textura estão ao alcance, pode definir, e para e para metade a precisão , resultando em apenas 16 bytes por vértice. Cortar os dados da malha ao meio permite-lhe carregar modelos maiores e potencialmente melhorar o desempenho.

## <a name="typical-use-cases"></a>Casos de uso típico

Encontrar boas definições de importação para um dado caso de utilização pode ser um processo tedioso. Por outro lado, as definições de conversão podem ter um impacto significativo no desempenho do tempo de execução.

Existem certas classes de casos de uso que se qualificam para otimizações específicas. Alguns exemplos são dados abaixo.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso de utilização: Visualização arquitetónica / grandes mapas exteriores

* Este tipo de cenas tendem a ser estáticas, o que significa que não precisam de peças móveis. Assim, o `sceneGraphMode` pode ser `static` definido `none`para ou mesmo , o que melhora o desempenho do tempo de execução. Com `static` o modo, o nó de raiz da cena ainda pode ser movido, rodado e escalado, por exemplo, para alternar dinamicamente entre 1:1 escala (para visualização na primeira pessoa) e uma vista de mesa.

* Quando precisa de mover peças, isso significa que normalmente também precisa de apoio para raios ou [outras consultas espaciais,](../../overview/features/spatial-queries.md)para que possa escolher essas peças em primeiro lugar. Por outro lado, se não pretende mover algo, é provável que também não precise que participe em consultas espaciais e, portanto, possa desligar a `generateCollisionMesh` bandeira. Este interruptor tem um impacto significativo nos tempos de conversão, tempos de carregamento e também custos de atualização do tempo de execução por quadro.

* Se a aplicação não utilizar `opaqueMaterialDefaultSidedness` [aviões cortados,](../../overview/features/cut-planes.md)a bandeira deve ser desligada. O ganho de desempenho é tipicamente de 20%-30%. Aviões cortados ainda podem ser usados, mas não haverá rostos traseiros quando olhar para as partes internas dos objetos, o que parece contraintuitivo. Para mais informações, consulte [a renderização unilateral](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Caso de utilização: Modelos de fotogrammetria

Ao renderizar modelos de fotogrammetria normalmente não há necessidade de `sceneGraphMode` `none`um gráfico de cena, para que possa definir o . Uma vez que estes modelos raramente contêm um gráfico de cena complexo para começar, o impacto desta opção deve ser insignificante, no entanto.

Como a iluminação já está cozida nas texturas, não é necessária iluminação dinâmica. Desta forma:

* Coloque `unlitMaterials` a `true` bandeira para transformar todos os materiais em materiais de cores não [iluminados](../../overview/features/color-materials.md).
* Remova os dados desnecessários do formato vértice. Veja o [exemplo](#example) acima.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Caso de utilização: Visualização de máquinas compactas, etc.

Nestes casos de utilização, os modelos têm frequentemente um detalhe muito elevado num pequeno volume. O renderizador está fortemente otimizado para lidar bem com estes casos. No entanto, a maioria das otimizações mencionadas no caso de utilização anterior não se aplicam aqui:

* As peças individuais devem ser selecionáveis `sceneGraphMode` e móveis, pelo que as peças devem ser deixadas para `dynamic`.
* Os moldes de raios são tipicamente parte integrante da aplicação, por isso as malshes de colisão devem ser geradas.
* Os aviões cortados ficam melhor com a `opaqueMaterialDefaultSidedness` bandeira ativada.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](model-conversion.md)
* [Materiais de cor](../../overview/features/color-materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)
* [Materiais de sobreposição durante a conversão do modelo](override-materials.md)
