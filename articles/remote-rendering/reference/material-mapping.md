---
title: Mapeamento de materiais para formatos de modelos
description: Descreve a conversão padrão dos formatos de origem do modelo para o material PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024049"
---
# <a name="material-mapping-for-model-formats"></a>Mapeamento de materiais para formatos de modelos

Quando um ativo de origem é [convertido como modelo,](../how-tos/conversion/model-conversion.md)o conversor cria [materiais](../concepts/materials.md) para cada [malha](../concepts/meshes.md). A forma como os materiais são criados pode ser [ultrapassada.](../how-tos/conversion/override-materials.md) No entanto, por padrão, a conversão criará [materiais PBR](../overview/features/pbr-materials.md). Uma vez que todos os formatos de ficheiros de origem, como o FBX, utilizam as suas próprias convenções para definir materiais, essas convenções devem ser mapeadas para os parâmetros de material PBR da Renderização Remota Azure. 

Este artigo lista os mapeamentos exatos usados para converter materiais de fonte para materiais de execução.

## <a name="gltf"></a>glTF

Quase tudo, desde a especificação glTF 2.0, é suportado na renderização remota de Azure, com exceção *do EmissiveFactor* e *daTextura Emissive*.

A tabela a seguir mostra o mapeamento:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTextura  |   albedoMap                |
|   metallicFactor    |   metalidão                |
|   textura metálica   |   metalnessMap             |
|   asperezaFactor   |   rugosidade                |
|   rugosidadeTextura  |   asperezaMap             |
|   oclusãoFactor   |   oclusão                |
|   oclusãoTextura  |   oclusionMap             |
|   normaltextura     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = falso, isTransparente = falso |
|   alphaMode.MASK    |   alphaClipEnabled = verdadeiro, éTransparente = falso  |
|   alphaMode.BLEND   |   isTransparente = verdadeiro     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissividadeTextura   |   -                        |

Cada textura no glTF pode ter um `texCoord` valor, que também é suportado nos materiais de renderização remota Azure.

### <a name="embedded-textures"></a>Texturas embutidas

As texturas incorporadas em *\* .bin* ou ficheiros *\* .glb* são suportadas.

### <a name="supported-gltf-extension"></a>Extensão de glTF suportada

Adicionalmente ao conjunto de funcionalidades base, a Azure Remote Rendering suporta as seguintes extensões glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Corresponde aos materiais de [cor.](../overview/features/color-materials.md) Para materiais *emissivos,* recomenda-se a utilização desta extensão.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Em vez de texturas metálicas-asperezas, você pode fornecer texturas difusas-specular-glossiness. A implementação de renderização remota Azure segue diretamente as fórmulas de conversão da extensão.

## <a name="fbx"></a>FBX

O formato FBX é de origem fechada e os materiais FBX não são compatíveis com materiais PBR em geral. A FBX utiliza uma descrição complexa de superfícies com muitos parâmetros e propriedades únicos e **nem todas são utilizadas pelo gasoduto de renderização remota Azure.**

> [!IMPORTANT]
> O pipeline de conversão do modelo de renderização remota Azure suporta apenas **FBX 2011 e superior**.

O formato FBX define uma abordagem conservadora para materiais, existem apenas dois tipos na especificação oficial do FBX:

* *Lambert* - Já não é habitualmente usado há algum tempo, mas ainda é apoiado pela conversão para Phong no tempo de conversão.
* *Phong* - Quase todos os materiais e ferramentas de conteúdo mais utilizados.

O modelo Phong é mais preciso e é usado como o *único* modelo para materiais FBX. Abaixo será referido como o *Material FBX*.

> Maya usa duas extensões personalizadas para FBX, definindo propriedades personalizadas para tipos de PBR e Stingray de um material. Estes detalhes não estão incluídos na especificação FBX, pelo que não é suportado pela Azure Remote Rendering atualmente.

Os materiais FBX usam o conceito Diffuse-Specular-SpecularLevel, para converter de uma textura difusa para um mapa de albedo precisamos calcular os outros parâmetros para subtraí-los da difusão.

> Todas as cores e texturas em FBX estão no espaço sRGB (também conhecido como espaço Gama), mas a renderização remota Azure funciona com espaço linear durante a visualização e no final do quadro converte tudo de volta ao espaço sRGB. O pipeline de renderização remota Azure converte tudo em espaço linear para enviá-lo como dados preparados para o renderizador.

Esta tabela mostra como as texturas são mapeadas de Materiais FBX para materiais de renderização remota Azure. Alguns deles não são usados diretamente, mas em combinação com outras texturas que participam nas fórmulas (por exemplo, a textura difusa):

| FBX | Azure Remote Rendering |
|:-----|:----|
| Cor ambiente | Mapa de Oclusão   |
| DifusoColor | *usado para Albedo, Metalness* |
| TransparenteColor | *usado para canal alfa de Albedo* |
| TransparênciaFactor | *usado para canal alfa de Albedo* |
| Opacidade | *usado para canal alfa de Albedo* |
| Espeleólogo | *usado para Albedo, Metalness, Aspereza* |
| SpecularFactor| *usado para Albedo, Metalness, Aspereza* |
| ShininessExponente | *usado para Albedo, Metalness, Aspereza* |
| Mapa normal | Mapa normal |
| Colisão | *convertido para NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflexãoColor | - |
| Mudança de DeslocamentoSColor | - |

O mapeamento acima é a parte mais complexa da conversão do material, devido a muitos pressupostos que têm de ser feitos. Discutimos estes pressupostos abaixo.

Algumas definições utilizadas abaixo:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 +  `Specular` . ∗ verde 0,7154 + `Specular` . ∗ Azul 0.0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Vermelho<sup>2</sup> + 0,587 * `Diffuse` . Verde<sup>2</sup> + 0,114 * `Diffuse` . Azul<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Vermelho<sup>2</sup> + 0,587 * `Specular` . Verde<sup>2</sup> + 0,114 * `Specular` . Azul<sup>2</sup>
* `SpecularStrength` = `Specular` máx. Vermelho, `Specular` . Verde, `Specular` . Azul)

A fórmula SpecularIntensity é obtida a partir [daqui.](https://en.wikipedia.org/wiki/Luma_(video))
A fórmula de brilho é descrita nesta [especificação](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Aspereza

`Roughness` é calculada a partir `Specular` e utilizando esta `ShininessExponent` [fórmula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). A fórmula é uma aproximação da aspereza do expoente specular Phong:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalidade

`Metalness` é calculada a partir `Diffuse` e utilizando esta fórmula a partir da `Specular` [especificação glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

A ideia aqui é que resolvemos a equação: Ax<sup>2</sup> + Bx + C = 0.
Basicamente, as superfícies dielétricas refletem cerca de 4% da luz de uma forma specular, e o resto é difuso. As superfícies metálicas não refletem a luz de uma forma difusa, mas tudo de forma specular.
Esta fórmula tem alguns inconvenientes, porque não há como distinguir entre plástico brilhante e superfícies metálicas brilhantes. Assumimos que a maior parte do tempo a superfície tem propriedades metálicas, e consequentemente superfícies de plástico/borracha brilhantes podem não parecer como esperado.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` é computado a partir de `Diffuse` `Specular` , e `Metalness` .

Como descrito na secção Metalness, as superfícies dielétricas refletem cerca de 4% da luz.  
A ideia aqui é interpolar linearmente entre `Dielectric` e `Metal` as cores usando o valor como `Metalness` fator. Se a metalometil for `0.0` , então, dependendo do specular, será uma cor escura (se o specular é alto) ou a difusão não mudará (se não houver specular). Se a metalidade é um grande valor, então a cor difusa desaparecerá em favor da cor specular.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` foi calculado pela fórmula acima, mas o canal alfa requer cálculos adicionais. O formato FBX é vago em relação à transparência e tem muitas formas de defini-lo. Diferentes ferramentas de conteúdo usam diferentes métodos. A ideia aqui é unificar-los numa fórmula. No entanto, torna alguns ativos incorretamente apresentados como transparentes, se não forem criados de forma comum.

Isto é calculado a partir `TransparentColor` `TransparencyFactor` de, `Opacity`

se `Opacity` for definido, em seguida, usá-lo diretamente: `AlbedoAlpha`  =  `Opacity` outra coisa  
se `TransparencyColor` for definido, então `AlbedoAlpha` = 1.0 - `TransparentColor` (. Vermelho + `TransparentColor` . Verde + `TransparentColor` . Azul) / 3.0) outra coisa  
se, `TransparencyFactor` então `AlbedoAlpha` = 1.0 - `TransparencyFactor`

A cor final `Albedo` tem quatro canais, combinando o `AlbedoRGB` com o `AlbedoAlpha` .

### <a name="summary"></a>Resumo

Resumindo aqui, `Albedo` estará muito perto do `Diffuse` original, se estiver perto de `Specular` zero. Caso contrário, a superfície será uma superfície metálica e perderá a cor difusa. A superfície ficará mais polida e refletora se `ShininessExponent` for grande o suficiente e for `Specular` brilhante. Caso contrário, a superfície ficará áspera e mal refletirá o ambiente.

### <a name="known-issues"></a>Problemas conhecidos

* A fórmula atual não funciona bem para uma geometria colorida simples. Se `Specular` for brilhante o suficiente, então todas as geometrias tornam-se superfícies metálicas refletoras sem qualquer cor. A solução aqui é baixar `Specular` para 30% do original ou utilizar a definição de conversão [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Os materiais PBR foram recentemente `Maya` adicionados e `3DS Max` ferramentas de criação de conteúdos. Eles usam propriedades de caixa preta definidas pelo utilizador personalizadas para passá-lo para FBX. A Azure Remote Rendering não lê essas propriedades adicionais porque não estão documentadas e o formato é de origem fechada.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](../how-tos/conversion/model-conversion.md)
* [Materiais dominantes durante a conversão do modelo](../how-tos/conversion/override-materials.md)
