---
title: Obter informações sobre conversões
description: Obter informações sobre conversões
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576630"
---
# <a name="get-information-about-conversions"></a>Obter informações sobre conversões

## <a name="information-about-a-conversion-the-result-file"></a>Informação sobre uma conversão: O ficheiro de resultados

Quando o serviço de conversão converte um ativo, escreve um resumo de quaisquer problemas num "ficheiro de resultados". Por exemplo, se um ficheiro `buggy.gltf` for convertido, o recipiente de saída conterá um ficheiro chamado `buggy.result.json` .

O ficheiro de resultados enumera quaisquer erros e advertências que ocorreram durante a conversão e dá um resumo de resultados, que é um dos `succeeded` , `failed` ou `succeeded with warnings` .
O ficheiro de resultados é estruturado como uma matriz de objetos JSON, cada um dos quais tem uma propriedade de cordas que é um dos `warning` , , , e `error` `internal warning` `internal error` `result` . Haverá no máximo um erro `error` (ou `internal error` ou) e haverá sempre um `result` .

## <a name="example-result-file"></a>Ficheiro *de resultados* de exemplo

O exemplo a seguir descreve uma conversão que gerou com sucesso um arrAsset. No entanto, uma vez que faltava uma textura, o arrAsset resultante pode não ser o pretendido.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Informação sobre um modelo convertido: O ficheiro de informação

O ficheiro arrAsset produzido pelo serviço de conversão destina-se exclusivamente ao consumo pelo serviço de prestação de serviços. Pode haver momentos, no entanto, em que pretende aceder a informações sobre um modelo sem iniciar uma sessão de renderização. Para suportar este fluxo de trabalho, o serviço de conversão coloca um ficheiro JSON ao lado do ficheiro arrAsset no recipiente de saída. Por exemplo, se um ficheiro `buggy.gltf` for convertido, o recipiente de saída conterá um ficheiro chamado `buggy.info.json` ao lado do ativo convertido `buggy.arrAsset` . Contém informações sobre o modelo de origem, o modelo convertido e sobre a própria conversão.

## <a name="example-info-file"></a>Arquivo *de informação* de exemplo

Aqui está um ficheiro de *informação* de exemplo produzido através da conversão de um ficheiro chamado `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informação no ficheiro informação

### <a name="the-files-section"></a>A secção *de ficheiros*

Esta secção contém os dados de ficheiros fornecidos.

* `input`: O nome do ficheiro de origem.
* `output`: O nome do ficheiro de saída, quando o utilizador tiver especificado um nome não padrão.

### <a name="the-conversionsettings-section"></a>A secção *conversõesSes*

Esta secção contém uma cópia das [conversões Especificadas](configure-model-conversion.md#settings-file) quando o modelo foi convertido.

### <a name="the-inputinfo-section"></a>A secção *inputInfo*

Esta secção regista informações sobre o formato de ficheiro de origem.

* `sourceAssetExtension`: A extensão do ficheiro de origem.
* `sourceAssetFormat`: Uma descrição do formato do ficheiro de origem.
* `sourceAssetFormatVersion`: A versão do formato de ficheiro de origem.
* `sourceAssetGenerator`: O nome da ferramenta que gerou o ficheiro de origem, se disponível.

### <a name="the-materialoverrides-section"></a>A secção *materialOverrides*

Esta secção fornece informações sobre [material sobrepor-se](override-materials.md) quando um ficheiro de sobreposição de material foi fornecido ao serviço de conversão.
Contém as seguintes informações:
* `numOverrides`: O número de entradas de substituição lidas a partir do ficheiro de substituição de material.
* `numOverriddenMaterials`: O número de materiais que foram ultrapassados.

### <a name="the-inputstatistics-section"></a>A secção *de inputStatistics*

Esta secção fornece informações sobre a cena da origem. Muitas vezes haverá discrepâncias entre os valores desta secção e os valores equivalentes na ferramenta que criou o modelo de origem. Estas diferenças são esperadas, uma vez que o modelo é modificado durante as etapas de exportação e conversão.

* `numMeshes`: O número de peças de malha, onde cada peça pode fazer referência a um único material.
* `numFaces`: O número total de _triângulos_ em todo o modelo. Note que a malha é triangulada durante a conversão. Este número contribui para o limite do polígono no tamanho padrão do [servidor de renderização](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: O número total de vértices em todo o modelo.
* `numMaterial`: O número total de materiais em todo o modelo.
* `numFacesSmallestMesh`: O número de triângulos na malha mais pequena do modelo.
* `numFacesBiggestMesh`: O número de triângulos na maior malha do modelo.
* `numNodes`: O número de nós no gráfico de cena do modelo.
* `numMeshUsagesInScene`: O número de vezes que os nós de referência. Mais do que um nó pode fazer referência à mesma malha.
* `maxNodeDepth`: A profundidade máxima dos nós dentro do gráfico de cena.

### <a name="the-outputinfo-section"></a>A secção *outputInfo*

Esta secção regista informações gerais sobre a saída gerada.

* `conversionToolVersion`: Versão do conversor de modelo.
* `conversionHash`: Um haxixe dos dados dentro do arrAsset que pode contribuir para a renderização. Pode ser usado para entender se o serviço de conversão produziu um resultado diferente quando reexecutado no mesmo ficheiro.

### <a name="the-outputstatistics-section"></a>A secção *de resultadosStatística*

Esta secção regista informações calculadas a partir do ativo convertido.

* `numMeshPartsCreated`: O número de malhas no arrAsset. Pode diferir `numMeshes` da secção, porque a `inputStatistics` instancing é afetada pelo processo de conversão.
* `numMeshPartsInstanced`: O número de malhas reutilizadas no arrAsset.
* `recenteringOffset`: Quando a `recenterToOrigin` opção nas [conversõesEção](configure-model-conversion.md) estiver ativada, este valor é a tradução que moveria o modelo convertido de volta à sua posição original.
* `boundingBox`: Os limites do modelo.

## <a name="deprecated-features"></a>Funcionalidades preteridas

O serviço de conversão escreve os ficheiros `stdout.txt` e o recipiente de `stderr.txt` saída, e estes tinham sido a única fonte de avisos e erros.
Estes ficheiros estão agora preprecados. Em vez disso, utilize [ficheiros de resultados](#information-about-a-conversion-the-result-file) para este fim.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](model-conversion.md)
* [Configurar a conversão de modelos](configure-model-conversion.md)
