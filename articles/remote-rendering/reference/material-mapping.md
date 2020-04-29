---
title: Mapeamento de materiais para formatos de modelos
description: Descreve a conversão padrão dos formatos de origem do modelo para o material PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680391"
---
# <a name="material-mapping-for-model-formats"></a>Mapeamento de materiais para formatos de modelos

Quando um ativo de origem é [convertido como modelo,](../how-tos/conversion/model-conversion.md)o conversor cria [materiais](../concepts/materials.md) para cada [malha](../concepts/meshes.md). A forma como os materiais são criados pode ser [ultrapassada.](../how-tos/conversion/override-materials.md) No entanto, por padrão, a conversão criará [materiais PBR](../overview/features/pbr-materials.md). Uma vez que todos os formatos de ficheiros de origem, como o FBX, utilizam as suas próprias convenções para definir materiais, essas convenções devem ser mapeadas para os parâmetros de materiais PBR da Renderização Remota Azure. 

Este artigo enumera os mapeamentos exatos usados para converter materiais de ativos de origem para materiais de tempo de execução.

## <a name="gltf"></a>glTF

Quase tudo, desde a especificação glTF 2.0, é suportado na Renderização Remota Azure, exceto *EmissiveFactor* e *EmissiveTexture*.

A tabela seguinte mostra o mapeamento:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   textura baseColor  |   albedoMap                |
|   metallicFactor    |   metalidade                |
|   textura metálica   |   metalnessMap             |
|   rugosidadeFactor   |   rugosidade                |
|   rugosidadeTextura  |   rugosidadeMapa             |
|   oclusãoFactor   |   oclusão                |
|   oclusãoTextura  |   oclusãoMap             |
|   textura normal     |   mapa normal                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alfaMode.OPAQUE  |   alphaClipEnabled = falso, isTransparent = falso |
|   alfaMode.MASK    |   alphaClipEnabled = verdadeiro, éTransparente = falso  |
|   alfaMode.BLEND   |   isTransparent = verdadeiro     |
|   duplaSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   textura emissiva   |   -                        |

Cada textura em glTF `texCoord` pode ter um valor, que também é suportado nos materiais de renderização remota Azure.

### <a name="embedded-textures"></a>Texturas embutidas

As texturas incorporadas em * \*ficheiros .bin* ou * \*.glb* são suportadas.

### <a name="supported-gltf-extension"></a>Extensão de glTF suportada

Além do conjunto de características base, a Renderização Remota Azure suporta as seguintes extensões glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)Corresponde a [materiais](../overview/features/color-materials.md)de cor . Para materiais *emissivos,* recomenda-se a utilização desta extensão.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Em vez de texturas metálicas de rugosidade, pode fornecer texturas difusas e especular-glossness. A implementação de renderização remota Azure segue diretamente as fórmulas de conversão da extensão.

## <a name="fbx"></a>FBX

O formato FBX é de fonte fechada e os materiais FBX não são compatíveis com materiais PBR em geral. O FBX usa uma descrição complexa de superfícies com muitos parâmetros e propriedades únicos e **nem todas são usadas pelo gasoduto de renderização remota Azure**.

> [!IMPORTANT]
> O pipeline de conversão do modelo de renderização remota Azure suporta apenas **FBX 2011 e superior**.

O formato FBX define uma abordagem conservadora para materiais, existem apenas dois tipos na especificação oficial fbX:

* *Lambert* - Já não é utilizado há algum tempo, mas continua a ser apoiado pela conversão para Phong no momento da conversão.
* *Phong* - Quase todos os materiais e a maioria das ferramentas de conteúdo usam este tipo.

O modelo Phong é mais preciso e é usado como o *único* modelo para materiais FBX. Abaixo será referido como o *Material FBX*.

> A Maya utiliza duas extensões personalizadas para fbX, definindo propriedades personalizadas para tipos de pBR e Stingray de um material. Estes detalhes não estão incluídos na especificação FBX, pelo que não é suportado pela Azure Remote Rendering atualmente.

Os materiais FBX utilizam o conceito Diffuse-Specular-SpecularLevel, de modo a converter de uma textura difusa para um mapa de albedo, precisamos calcular os outros parâmetros para subtraí-los de difusão.

> Todas as cores e texturas em FBX estão no espaço SRGB (também conhecido como espaço Gama), mas a Renderização Remota Azure trabalha com espaço linear durante a visualização e no final da moldura converte tudo de volta ao espaço SRGB. O pipeline de ativos de renderização remota Azure converte tudo em espaço linear para enviá-lo como dados preparados para o renderizador.

Esta tabela mostra como as texturas são mapeadas de materiais FBX para materiais de renderização remota Azure. Algumas delas não são diretamente utilizadas, mas em combinação com outras texturas que participam nas fórmulas (por exemplo, a textura difusa):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Mapa de Oclusão   |
| DiffuseColor | *usado para Albedo, Metalness* |
| Transparência | *usado para o canal alfa de Albedo* |
| TransparênciaFactor | *usado para o canal alfa de Albedo* |
| Opacidade | *usado para o canal alfa de Albedo* |
| Colorte Specular | *usado para Albedo, Metalness, Aspereza* |
| SpecularFactor| *usado para Albedo, Metalness, Aspereza* |
| ShininessExponent | *usado para Albedo, Metalness, Aspereza* |
| Mapa Normal | Mapa Normal |
| Lombada | *convertido para NormalMap* |
| Cor Emissa | - |
| EmissiveFactor | - |
| ReflexãoColor | - |
| Cores de deslocamento | - |

O mapeamento acima é a parte mais complexa da conversão material, devido a muitos pressupostos que têm de ser feitos. Discutimos estes pressupostos abaixo.

Algumas definições utilizadas abaixo:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Vermelho0.2125 + `Specular`. Verde0.7154 + `Specular`. Azul 0.0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Vermelho<sup>2</sup> + 0,587 * `Diffuse`. Verde<sup>2</sup> + 0,114 * `Diffuse`. Azul<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Vermelho<sup>2</sup> + 0,587 * `Specular`. Verde<sup>2</sup> + 0,114 * `Specular`. Azul<sup>2</sup>
* `SpecularStrength`= máximo.`Specular` Vermelho, `Specular`. Verde, `Specular`. Azul)

A fórmula SpecularIntensity é obtida a partir [daqui.](https://en.wikipedia.org/wiki/Luma_(video))
A fórmula de brilho é descrita nesta [especificação.](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)

### <a name="roughness"></a>Aspereza

`Roughness`é calculado `Specular` `ShininessExponent` a partir e utilizando [esta fórmula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). A fórmula é uma aproximação de aspereza do expoente specular de Phong:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalidade

`Metalness`é calculada `Diffuse` `Specular` a partir e utilizando esta [fórmula a partir da especificação glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

A ideia aqui é que resolvemos a equação: Ax<sup>2</sup> + Bx + C = 0.
Basicamente, as superfícies dielétricas refletem cerca de 4% da luz de uma forma especular, e o resto é difuso. As superfícies metálicas não refletem a luz de uma forma difusa, mas tudo de forma especular.
Esta fórmula tem algumas desvantagens, porque não há como distinguir entre plástico brilhante e superfícies metálicas brilhantes. Assumimos que a maior parte do tempo a superfície tem propriedades metálicas, e consequentemente superfícies de plástico/borracha brilhantes podem não parecer como esperado.
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

### <a name="albedo"></a>Rio Albedo

`Albedo`é computada `Specular`a `Metalness`partir de, `Diffuse`e .

Como descrito na secção Metalness, as superfícies dielétricas refletem cerca de 4% da luz.  
A ideia aqui é linearmente `Dielectric` interpolar entre e `Metal` cores usando o `Metalness` valor como fator. Se a `0.0`metalidade for , então, dependendo do espectro, será ou uma cor escura (se o espectro é alto) ou difuso não mudará (se não houver especular). Se a metalidade é um grande valor, então a cor difusa desaparecerá em favor da cor especular.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`foi calculado pela fórmula acima, mas o canal alfa requer cálculos adicionais. O formato FBX é vago em relação à transparência e tem muitas formas de defini-lo. Diferentes ferramentas de conteúdo utilizam métodos diferentes. A ideia aqui é unificar-los numa fórmula. No entanto, torna alguns ativos incorretamente mostrados como transparentes, se não forem criados de forma comum.

Isto é calculado `TransparentColor` `TransparencyFactor`a `Opacity`partir de, :

se `Opacity` for definido, use-o `AlbedoAlpha`  =  `Opacity` diretamente: então  
se `TransparencyColor` for definido, então `AlbedoAlpha` =`TransparentColor`1,0 - (. Vermelho `TransparentColor`+ . Verde `TransparentColor`+ . Azul) / 3.0) mais  
se, `TransparencyFactor` `AlbedoAlpha` então = 1,0 -`TransparencyFactor`

A `Albedo` cor final tem quatro `AlbedoRGB` canais, combinando o com o `AlbedoAlpha`.

### <a name="summary"></a>Resumo

Resumindo aqui, `Albedo` estará muito perto `Diffuse`do `Specular` original, se estiver perto de zero. Caso contrário, a superfície parecerá uma superfície metálica e perderá a cor difusa. A superfície parecerá mais `ShininessExponent` polida e `Specular` reflexiva se for grande o suficiente e brilhante. Caso contrário, a superfície parecerá áspera e mal refletirá o ambiente.

### <a name="known-issues"></a>Problemas conhecidos

* A fórmula atual não funciona bem para geometria simples colorida. Se `Specular` for suficientemente brilhante, todas as geometrias tornam-se superfícies metálicas refletoras sem qualquer cor. A suposição aqui `Specular` é baixar para 30% a partir do original ou utilizar a definição de conversão [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Os materiais PBR `Maya` `3DS Max` foram recentemente adicionados e ferramentas de criação de conteúdo. Usam propriedades de caixa preta definidas pelo utilizador para passá-la para FBX. A Renderização Remota Azure não lê essas propriedades adicionais porque não estão documentadas e o formato é de origem fechada.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](../how-tos/conversion/model-conversion.md)
* [Materiais overover durante a conversão do modelo](../how-tos/conversion/override-materials.md)
