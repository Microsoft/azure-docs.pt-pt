---
title: Obter informações sobre um modelo convertido
description: Descrição de todos os parâmetros de conversão do modelo
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681522"
---
# <a name="get-information-about-a-converted-model"></a>Obter informações sobre um modelo convertido

O ficheiro arrAsset produzido pelo serviço de conversão destina-se exclusivamente ao consumo pelo serviço de renderização. Pode haver momentos, no entanto, em que pretende aceder a informações sobre um modelo sem iniciar uma sessão de renderização. Por isso, o serviço de conversão coloca um ficheiro JSON ao lado do ficheiro arrAsset no recipiente de saída. Por exemplo, se `buggy.gltf` um ficheiro for convertido, o `buggy.info.json` recipiente de `buggy.arrAsset`saída conterá um ficheiro chamado ao lado do ativo convertido . Contém informações sobre o modelo de origem, o modelo convertido, e sobre a conversão em si.

## <a name="example-info-file"></a>Arquivo *de informações* de exemplo

Aqui está um arquivo de *informação* de `buggy.gltf`exemplo produzido através da conversão de um ficheiro chamado:

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

## <a name="information-in-the-info-file"></a>Informação no ficheiro de informação

### <a name="the-files-section"></a>A secção *de ficheiros*

Esta secção contém os nomes de ficheiros fornecidos.

* `input`: O nome do ficheiro fonte.
* `output`: O nome do ficheiro de saída, quando o utilizador tiver especificado um nome não predefinido.

### <a name="the-conversionsettings-section"></a>A secção *definições de conversão*

Esta secção contém uma cópia das [Definições](configure-model-conversion.md#settings-file) de Conversão especificadas quando o modelo foi convertido.

### <a name="the-inputinfo-section"></a>A secção *inputInfo*

Esta secção regista informações sobre o formato de ficheiro de origem.

* `sourceAssetExtension`: A extensão do ficheiro de origem.
* `sourceAssetFormat`: Uma descrição do formato de ficheiro fonte.
* `sourceAssetFormatVersion`: A versão do formato de ficheiro fonte.
* `sourceAssetGenerator`: O nome da ferramenta que gerou o ficheiro fonte, se disponível.

### <a name="the-inputstatistics-section"></a>A secção *inputStatistics*

Esta secção fornece informações sobre a cena da origem. Muitas vezes haverá discrepâncias entre os valores desta secção e os valores equivalentes na ferramenta que criou o modelo de origem. Esperam-se tais diferenças, uma vez que o modelo é modificado durante as etapas de exportação e conversão.

* `numMeshes`: O número de peças de malha, onde cada peça pode fazer referência a um único material.
* `numFaces`: O número total de _triângulos_ em todo o modelo. Note que a malha é triangulada durante a conversão.
* `numVertices`: O número total de vértices em todo o modelo.
* `numMaterial`: O número total de materiais em todo o modelo.
* `numFacesSmallestMesh`: O número de triângulos na malha mais pequena do modelo.
* `numFacesBiggestMesh`: O número de triângulos na maior malha do modelo.
* `numNodes`: O número de nós no gráfico de cena do modelo.
* `numMeshUsagesInScene`: O número de vezes que não faz nódeas referências. Mais do que um nó pode referir a mesma malha.
* `maxNodeDepth`: A profundidade máxima dos nódosos dentro do gráfico de cena.

### <a name="the-outputinfo-section"></a>A secção *outputInfo*

Esta secção regista informações gerais sobre a saída gerada.

* `conversionToolVersion`: Versão do conversor do modelo.
* `conversionHash`: Um hash dos dados dentro do arrAsset que pode contribuir para a renderização. Pode ser usado para entender se o serviço de conversão produziu um resultado diferente quando reexecutado no mesmo ficheiro.

### <a name="the-outputstatistics-section"></a>A secção *de estatísticas de saída*

Esta secção regista informações calculadas a partir do ativo convertido.

* `numMeshPartsCreated`: O número de medas no arrAsset. Pode diferir `numMeshes` da `inputStatistics` secção, porque o inestacção é afetado pelo processo de conversão.
* `numMeshPartsInstanced`: O número de medas que são reutilizadas no arrAsset.
* `recenteringOffset`: Quando `recenterToOrigin` a opção nas [Definições](configure-model-conversion.md) de Conversão está ativada, este valor é a tradução que faria com que o modelo convertido voltasse à sua posição original.
* `boundingBox`: Os limites do modelo.

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](model-conversion.md)
* [Configurar a conversão de modelos](configure-model-conversion.md)
