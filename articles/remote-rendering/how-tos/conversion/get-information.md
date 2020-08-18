---
title: Obter informações sobre um modelo convertido
description: Descrição de todos os parâmetros de conversão do modelo
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: f5c38ac88503416b37b720a091c9e46d819a3146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509302"
---
# <a name="get-information-about-a-converted-model"></a>Obter informações sobre um modelo convertido

O ficheiro arrAsset produzido pelo serviço de conversão destina-se exclusivamente ao consumo pelo serviço de prestação de serviços. Pode haver momentos, no entanto, em que pretende aceder a informações sobre um modelo sem iniciar uma sessão de renderização. Por isso, o serviço de conversão coloca um ficheiro JSON ao lado do ficheiro arrAsset no recipiente de saída. Por exemplo, se um ficheiro `buggy.gltf` for convertido, o recipiente de saída conterá um ficheiro chamado `buggy.info.json` ao lado do ativo convertido `buggy.arrAsset` . Contém informações sobre o modelo de origem, o modelo convertido e sobre a própria conversão.

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

## <a name="next-steps"></a>Passos seguintes

* [Conversão de modelo](model-conversion.md)
* [Configurar a conversão de modelos](configure-model-conversion.md)
