---
title: Configurar a conversão de modelos
description: Descrição de todos os parâmetros de conversão do modelo
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 2134dde0fa0b92bec4519c0d6a24dcaad3792baa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575185"
---
# <a name="configure-the-model-conversion"></a>Configurar a conversão de modelos

Este capítulo documenta as opções para a conversão do modelo.

## <a name="settings-file"></a>Ficheiro de definições

Se um ficheiro chamado `<modelName>.ConversionSettings.json` for encontrado no recipiente de entrada ao lado do modelo de `<modelName>.<ext>` entrada, então será utilizado para fornecer uma configuração adicional para o processo de conversão do modelo.
Por exemplo, `box.ConversionSettings.json` seria utilizado na conversão `box.gltf` .

O conteúdo do ficheiro deve satisfazer o seguinte esquema json:

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
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Um ficheiro de exemplo `box.ConversionSettings.json` pode ser:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Parâmetros de geometria

* `scaling` - Este parâmetro escala um modelo uniformemente. A escala pode ser usada para crescer ou encolher um modelo, por exemplo, para exibir um modelo de construção em cima de uma mesa.
A escala também é importante quando um modelo é definido em unidades que não os metros, uma vez que o motor de renderização espera contadores.
Por exemplo, se um modelo for definido em centímetros, então a aplicação de uma escala de 0,01 deve tornar o modelo no tamanho correto.
Alguns formatos de dados de origem (por exemplo.fbx) fornecem uma sugestão de escala de unidade, caso em que a conversão escala implicitamente o modelo para as unidades de contador. A escala implícita fornecida pelo formato de origem será aplicada em cima do parâmetro de escala.
O fator de escala final é aplicado aos vértices de geometria e às transformações locais dos nós gráficos de cena. A escala para a transformação da entidade raiz permanece não modificada.

* `recenterToOrigin` - Estabelece que um modelo deve ser convertido de modo a que a sua caixa de delimitação esteja centrada na origem.
Se um modelo de origem for deslocado longe da origem, problemas de precisão de ponto flutuante podem causar artefactos de renderização.
Centrar o modelo pode ajudar nesta situação.

* `opaqueMaterialDefaultSidedness` - O motor de renderização pressupõe que os materiais opacos são de duplala.
se essa suposição não for verdadeira de um modelo específico, este parâmetro deve ser definido como "SingleSided". Para mais informações, consulte [ :::no-loc text="single sided"::: renderização.](../../overview/features/single-sided-rendering.md)

### <a name="material-de-duplication"></a>Des duplicação de material

* `deduplicateMaterials` - Este parâmetro permite ou desativa a des duplicação automática de materiais que partilham as mesmas propriedades e texturas. A des duplicação ocorre após a substituição do material. É ativado por defeito.

* Se mesmo após a des duplicação um modelo tiver mais de 65.535 materiais, o serviço tentará fundir materiais com propriedades semelhantes. Em último recurso, quaisquer materiais que excedam o limite serão substituídos por um material de erro vermelho.

![A imagem mostra dois cubos de 68.921 triângulos coloridos.](media/mat-dedup.png?raw=true)

Dois cubos de 68.921 triângulos coloridos. Esquerda: Antes da des duplicação com 68.921 materiais de cor. Direito: Após a des duplicação com 64.000 materiais de cor. O limite é de 65.535 materiais. (Ver [limites](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Parâmetros do espaço de cor

O motor de renderização espera que os valores de cor estejam no espaço linear.
Se um modelo é definido usando espaço gama, então estas opções devem ser definidas como verdadeiras.

* `gammaToLinearMaterial` - Converter cores de material do espaço gama para o espaço linear
* `gammaToLinearVertex` - Converter :::no-loc text="vertex"::: cores do espaço gama para o espaço linear

> [!NOTE]
> Para os ficheiros FBX, estas definições são definidas `true` por predefinição. Para todos os outros tipos de ficheiros, o padrão é `false` .

### <a name="scene-parameters"></a>Parâmetros de cena

* `sceneGraphMode` - Define como o gráfico de cena no ficheiro de origem é convertido:
  * `dynamic` (predefinição): Todos os objetos do ficheiro são expostos como [entidades](../../concepts/entities.md) na API e podem ser transformados de forma independente. A hierarquia do nó em tempo de execução é idêntica à estrutura do ficheiro de origem.
  * `static`: Todos os objetos estão expostos na API, mas não podem ser transformados de forma independente.
  * `none`: O gráfico da cena é desmoronado num único objeto.

Cada modo tem um desempenho de tempo de execução diferente. Em `dynamic` modo, o custo de desempenho escala linearmente com o número de [entidades](../../concepts/entities.md) no gráfico, mesmo quando nenhuma parte é movida. Utilize o modo de utilização `dynamic` apenas quando for necessário mover peças individualmente, por exemplo, para uma animação de "vista de explosão".

O `static` modo exporta o gráfico de cena completa, mas as partes dentro deste gráfico têm uma transformação constante em relação à sua parte raiz. O nó raiz do objeto, no entanto, ainda pode ser movido, rodado ou escalado sem custos significativos de desempenho. Além disso, [as consultas espaciais](../../overview/features/spatial-queries.md) devolverão peças individuais e cada parte pode ser modificada através de [sobreposições de estado.](../../overview/features/override-hierarchical-state.md) Com este modo, a sobrecarga de tempo de execução por objeto é insignificante. É ideal para grandes cenas onde ainda precisa de inspeção por objeto, mas nenhuma transformação por objeto muda.

O `none` modo tem menos tempo de funcionamento e também tempos de carregamento ligeiramente melhores. A inspeção ou a transformação de objetos individuais não é possível neste modo. Os casos de utilização são, por exemplo, modelos de fotogrammetria que não têm um gráfico de cena significativo em primeiro lugar.

> [!TIP]
> Muitas aplicações carregarão vários modelos. Deve otimizar os parâmetros de conversão de cada modelo, dependendo da forma como será utilizado. Por exemplo, se pretender exibir o modelo de um carro para que o utilizador se desmonte e inspecione em detalhe, tem de o converter com `dynamic` modo. No entanto, se quiser colocar o carro num ambiente de sala de espetáculos, esse modelo pode ser convertido com `sceneGraphMode` conjunto para ou mesmo `static` `none` .

### <a name="physics-parameters"></a>Parâmetros da física

* `generateCollisionMesh` - Se necessitar de apoio para [consultas espaciais](../../overview/features/spatial-queries.md) num modelo, esta opção tem de ser ativada. No pior dos casos, a criação de uma malha de colisão pode duplicar o tempo de conversão. Os modelos com malhas de colisão demoram mais tempo a carregar e quando utilizam um `dynamic` gráfico de cena, também têm uma maior sobrecarga de desempenho. Para um desempenho geral ideal, deve desativar esta opção em todos os modelos em que não necessita de consultas espaciais.

### <a name="unlit-materials"></a>Materiais não iluminados

* `unlitMaterials` - Por predefinição, a conversão preferirá criar [materiais PBR](../../overview/features/pbr-materials.md). Esta opção diz ao conversor que trate todos os materiais como [materiais de cor.](../../overview/features/color-materials.md) Se tem dados que já incorporam iluminação, como modelos criados através da fotogrammetria, esta opção permite-lhe impor rapidamente a conversão correta para todos os materiais, sem a necessidade de [sobrepor cada material](override-materials.md) individualmente.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversão de formatos FBX mais antigos, com um modelo de material Phong

* `fbxAssumeMetallic` - Versões mais antigas do formato FBX definem os seus materiais utilizando um modelo de material Phong. O processo de conversão tem de inferir como estes materiais mapeiam para o [modelo PBR](../../overview/features/pbr-materials.md)do renderizador. Normalmente isto funciona bem, mas uma ambiguidade pode surgir quando um material não tem texturas, valores espumosos elevados e uma cor albedo não-cinza. Nestas circunstâncias, a conversão tem de escolher entre priorizar os elevados valores espeleculares, definir um material metálico altamente reflexivo onde a cor do albedo se dissolve, ou priorizar a cor do albedo, definindo algo como um plástico colorido brilhante. Por predefinição, o processo de conversão pressupõe que valores altamente espumosos implicam um material metálico nos casos em que a ambiguidade se aplica. Este parâmetro pode ser definido `false` para mudar para o oposto.

### <a name="coordinate-system-overriding"></a>Sistema de coordenadas sobressaíndo

* `axis` - Para anular a coordenada unidade-vectores do sistema. Os valores predefinidos são `["+x", "+y", "+z"]` . Em teoria, o formato FBX tem um cabeçalho onde esses vetores são definidos e a conversão usa essa informação para transformar a cena. O formato glTF também define um sistema de coordenadas fixa. Na prática, alguns ativos têm informações incorretas no seu cabeçalho ou foram salvos com uma convenção de sistema de coordenadas diferente. Esta opção permite-lhe anular o sistema de coordenadas para compensar. Por exemplo: `"axis" : ["+x", "+z", "-y"]` irá trocar o eixo Z e o eixo Y e manter a mão do sistema de coordenadas invertendo a direção do eixo Y.

### <a name="node-meta-data"></a>Dados de meta de nó

* `metadataKeys` - Permite especificar as teclas das propriedades dos metadados de nó que pretende manter no resultado da conversão. Pode especificar as chaves exatas ou as chaves wildcard. As teclas Wildcard são do formato "ABC*" e correspondem a qualquer chave que comece com "ABC". Os tipos de valor dos metadados suportados `bool` `int` `float` são, `string` e.

    Para ficheiros GLTF estes dados provêm do [objeto extras nos nós](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Para ficheiros FBX estes dados provêm dos `Properties70` dados em `Model nodes` . Consulte a documentação da sua Ferramenta de Ativo 3D para mais detalhes.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: formato

É possível ajustar o :::no-loc text="vertex"::: formato de uma malha, para trocar precisão por poupança de memória. Uma pegada de memória mais baixa permite-lhe carregar modelos maiores ou obter um melhor desempenho. No entanto, dependendo dos seus dados, o formato errado pode impactar significativamente a qualidade de renderização.

> [!CAUTION]
> Mudar o :::no-loc text="vertex"::: formato deve ser um último recurso quando os modelos já não se encaixam na memória, ou quando otimizam para o melhor desempenho possível. As mudanças podem facilmente introduzir artefactos de renderização, tanto óbvios como subtis. A menos que saiba o que procurar, não deve mudar o padrão.

Estes ajustes são possíveis:

* Fluxos de dados específicos podem ser explicitamente incluídos ou excluídos.
* A precisão dos fluxos de dados pode ser diminuída para reduzir a pegada de memória.

A `vertex` seguinte secção no `.json` ficheiro é opcional. Para cada parte que não esteja explicitamente especificada, o serviço de conversão volta à sua definição predefinida.

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

Ao forçar um componente a `NONE` , é garantido que a malha de saída não tem o respetivo fluxo.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Formatos de componentes por :::no-loc text="vertex"::: fluxo

Estes formatos são permitidos para os respetivos componentes:

| :::no-loc text="Vertex"::: | Formatos suportados (arrojado = padrão) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|cor0| **8_8_8_8_UNSIGNED_NORMALIZED**, NENHUMA |
|cor1| 8_8_8_8_UNSIGNED_NORMALIZED, **NENHUMA**|
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUMA |
|tangente| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUMA |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NENHUMA |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NENHUMA |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NENHUMA |

#### <a name="supported-component-formats"></a>Formatos de componentes suportados

As pegadas de memória dos formatos são as seguintes:

| Formato | Descrição | Bytes per :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|precisão de ponto flutuante completo de dois componentes|8
|16_16_FLOAT|precisão de ponto flutuante metade de dois componentes|4
|32_32_32_FLOAT|precisão de ponto flutuante completo de três componentes|12
|16_16_16_16_FLOAT|precisão de ponto flutuante metade de quatro componentes|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte de quatro componentes, normalizado ao `[0; 1]` alcance|4
|8_8_8_8_SIGNED_NORMALIZED|byte de quatro componentes, normalizado ao `[-1; 1]` alcance|4

#### <a name="best-practices-for-component-format-changes"></a>Melhores práticas para alterações no formato de componentes

* `position`: É raro que a precisão reduzida seja suficiente. **16_16_16_16_FLOAT** introduz artefactos de quantificação percetíveis, mesmo para pequenos modelos.
* `normal``tangent`, `binormal` " Tipicamente estes valores são alterados em conjunto. A menos que existam artefactos de iluminação visíveis que resultem de uma quantificação normal, não há razão para aumentar a sua precisão. Em alguns casos, porém, estes componentes podem ser definidos para **NENHUM**:
  * `normal`, `tangent` e só são `binormal` necessários quando pelo menos um material do modelo deve ser aceso. No ARR é o caso quando um [material PBR](../../overview/features/pbr-materials.md) é usado no modelo a qualquer momento.
  * `tangent` e `binormal` só são necessários quando qualquer um dos materiais iluminados usa uma textura de mapa normal.
* `texcoord0`, `texcoord1` : As coordenadas de textura podem utilizar uma precisão reduzida **(16_16_FLOAT)** quando os seus valores permanecem na `[0; 1]` gama e quando as texturas endereçadas têm um tamanho máximo de 2048 x 2048 pixels. Se esses limites forem ultrapassados, a qualidade do mapeamento de textura sofrerá.

#### <a name="example"></a>Exemplo

Assuma que tem um modelo de fotogrammetria, que tem iluminação assada nas texturas. Tudo o que é necessário para tornar o modelo são :::no-loc text="vertex"::: posições e coordenadas de textura.

Por predefinição, o conversor tem de assumir que poderá querer utilizar materiais PBR num modelo em algum momento, pelo que irá gerar `normal` `tangent` , e `binormal` dados para si. Consequentemente, o uso da memória por vértice é `position` (12 bytes) + `texcoord0` (8 bytes) + `normal` (4 bytes) + `tangent` (4 bytes) + `binormal` (4 bytes) = 32 bytes. Modelos maiores deste tipo podem facilmente ter muitos milhões de :::no-loc text="vertices"::: resultantes em modelos que podem ocupar vários gigabytes de memória. Tais grandes quantidades de dados afetarão o desempenho e pode até ficar sem memória.

Sabendo que nunca precisa de iluminação dinâmica no modelo, e sabendo que todas as coordenadas de textura estão ao `[0; 1]` alcance, pode `normal` `tangent` definir, e `binormal` para e para a meia `NONE` `texcoord0` precisão ( ), `16_16_FLOAT` resultando em apenas 16 bytes :::no-loc text="vertex"::: por. Cortar os dados de malha ao meio permite carregar modelos maiores e potencialmente melhorar o desempenho.

## <a name="memory-optimizations"></a>Otimizações de memória

O consumo de memória de conteúdos carregados pode tornar-se um estrangulamento no sistema de renderização. Se a carga útil da memória se tornar demasiado grande, pode comprometer o desempenho da renderização ou fazer com que o modelo não carregue completamente. Este parágrafo discute algumas estratégias importantes para reduzir a pegada da memória.

### <a name="instancing"></a>Instancing

O instancing é um conceito em que as malhas são reutilizadas para peças com transformações espaciais distintas, em oposição a todas as partes que referenciam a sua própria geometria única. A instancing tem um impacto significativo na pegada da memória.
Por exemplo, as caixas de utilização para a instalação são os parafusos de um modelo de motor ou cadeiras num modelo arquitetónico.

> [!NOTE]
> A instancing pode melhorar significativamente o consumo de memória (e, portanto, os tempos de carregamento) no entanto, as melhorias no lado do desempenho da renderização são insignificantes.

O serviço de conversão respeita a instancing se as peças estiverem marcadas em conformidade no ficheiro de origem. No entanto, a conversão não realiza uma análise profunda adicional dos dados de malha para identificar peças reutilizáveis. Assim, a ferramenta de criação de conteúdos e o seu gasoduto de exportação são os critérios decisivos para uma correta configuração de instalação.

Uma forma simples de testar se a informação de instancing é preservada durante a conversão é ter uma olhada nas estatísticas de [resultados](get-information.md#example-info-file), especificamente o `numMeshPartsInstanced` membro. Se o valor `numMeshPartsInstanced` for maior do que zero, indica que as malhas são partilhadas entre instâncias.

#### <a name="example-instancing-setup-in-3ds-max"></a>Exemplo: Configuração de instancing em 3ds Max

[O Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) tem modos distintos de clonagem de objetos chamados **`Copy`** , e que se **`Instance`** **`Reference`** comportam de forma diferente no que diz respeito à instancing no `.fbx` ficheiro exportado.

![Clonagem em 3ds Max](./media/3dsmax-clone-object.png)

* **`Copy`** : Neste modo, a malha é clonada, pelo que não é utilizada a instalação `numMeshPartsInstanced` (= 0).
* **`Instance`** : Os dois objetos partilham a mesma malha, pelo que é utilizada a instancing `numMeshPartsInstanced` (= 1).
* **`Reference`** : Modificadores distintos podem ser aplicados às geometrias, pelo que o exportador escolhe uma abordagem conservadora e não utiliza a instancing `numMeshPartsInstanced` (= 0).


### <a name="depth-based-composition-mode"></a>Modo de composição baseado em profundidade

Se a memória for uma preocupação, configuure o renderizador com o [modo de composição baseado em profundidade](../../concepts/rendering-modes.md#depthbasedcomposition-mode). Neste modo, a carga útil da GPU é distribuída por várias GPUs.

### <a name="decrease-vertex-size"></a>Diminuir o tamanho do vértice

Como discutido nas [melhores práticas para alterações de formato de componente,](configure-model-conversion.md#best-practices-for-component-format-changes) ajustar o formato vértice pode diminuir a pegada de memória. No entanto, esta opção deve ser o último recurso.

### <a name="texture-sizes"></a>Tamanhos de textura

Dependendo do tipo de cenário, a quantidade de dados de textura pode superar a memória utilizada para os dados de malha. Os modelos de fotogrammetria são candidatos.
A configuração de conversão não fornece uma forma de reduzir automaticamente as texturas. Se necessário, o dimensionamento da textura tem de ser feito como um passo de pré-processamento do lado do cliente. No entanto, o passo de conversão escolhe um formato adequado [de compressão de textura:](https://docs.microsoft.com/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)

* `BC1` para texturas de cor opacas
* `BC7` para texturas de cores de origem com canal alfa

Uma vez que o formato `BC7` tem o dobro da pegada de memória em comparação `BC1` com, é importante ter certeza de que as texturas de entrada não fornecem um canal alfa desnecessariamente.

## <a name="typical-use-cases"></a>Casos de uso típicos

Encontrar boas definições de importação para um dado caso de utilização pode ser um processo enfadonho. Por outro lado, as definições de conversão podem ter um impacto significativo no desempenho do tempo de execução.

Existem certas classes de casos de uso que se qualificam para otimizações específicas. Alguns exemplos são dados abaixo.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Caso de utilização: Visualização arquitetónica / grandes mapas ao ar livre

* Este tipo de cenas tendem a ser estáticas, o que significa que não precisam de peças móveis. Assim, o `sceneGraphMode` pode ser definido para ou mesmo , o que `static` `none` melhora o desempenho do tempo de execução. Com `static` o modo, o nó raiz da cena ainda pode ser movido, rodado e dimensionado, por exemplo, para alternar dinamicamente entre a escala de 1:1 (para a vista da primeira pessoa) e uma vista superior de mesa.

* Quando precisa de mover peças, isso normalmente também significa que precisa de apoio para raycasts ou outras [consultas espaciais](../../overview/features/spatial-queries.md), para que possa escolher essas peças em primeiro lugar. Por outro lado, se não pretende mover algo, é provável que também não precise que participe em consultas espaciais e, portanto, possa desligar a `generateCollisionMesh` bandeira. Este interruptor tem um impacto significativo nos tempos de conversão, tempos de carregamento e também nos custos de atualização do tempo de funcionamento por quadro.

* Se a aplicação não utilizar [aviões cortados,](../../overview/features/cut-planes.md)a `opaqueMaterialDefaultSidedness` bandeira deve ser desligada. O ganho de desempenho é tipicamente de 20%-30%. Os planos cortados ainda podem ser usados, mas não haverá faces traseiras quando olharem para as partes internas dos objetos, que parecem contraintuitivas. Para mais informações, consulte [ :::no-loc text="single sided"::: renderização.](../../overview/features/single-sided-rendering.md)

### <a name="use-case-photogrammetry-models"></a>Caso de utilização: Modelos de fotogrammetria

Ao renderizar modelos de fotogrammetria, normalmente não há necessidade de um gráfico de cena, para que possa definir `sceneGraphMode` o `none` . Uma vez que estes modelos raramente contêm um gráfico de cena complexo para começar, o impacto desta opção deve ser insignificante, no entanto.

Como a iluminação já está cozida nas texturas, não é necessária iluminação dinâmica. Desta forma:

* Coloque a `unlitMaterials` bandeira para transformar todos os `true` materiais em materiais de cores não [iluminados](../../overview/features/color-materials.md).
* Remova dados não desprotestos do formato vértice. Veja o [exemplo](#example) acima.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Estojo de utilização: Visualização de máquinas compactas, etc.

Nestes casos de utilização, os modelos têm frequentemente detalhes muito elevados dentro de um pequeno volume. O renderizador está fortemente otimizado para lidar bem com estes casos. No entanto, a maioria das otimizações mencionadas no caso de utilização anterior não se aplicam aqui:

* As peças individuais devem ser selecionáveis e móveis, pelo que `sceneGraphMode` a deve ser deixada para `dynamic` .
* Os moldes de raios são tipicamente parte integrante da aplicação, por isso as malhas de colisão devem ser geradas.
* Os aviões cortados ficam melhor com a `opaqueMaterialDefaultSidedness` bandeira ativada.

## <a name="deprecated-features"></a>Funcionalidades preteridas

O fornecimento de definições que utilizem o nome de ficheiro não específico do modelo `conversionSettings.json` ainda é suportado, mas prectado.
Utilize o nome de ficheiro específico do `<modelName>.ConversionSettings.json` modelo.

A utilização de uma `material-override` definição para identificar um [ficheiro de substituição de material](override-materials.md) no ficheiro de definições de conversão ainda é suportada, mas preprecada. Utilize o nome de ficheiro específico do `<modelName>.MaterialOverrides.json` modelo.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](model-conversion.md)
* [Materiais de cor](../../overview/features/color-materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)
* [Substituir materiais durante a conversão de modelos](override-materials.md)
