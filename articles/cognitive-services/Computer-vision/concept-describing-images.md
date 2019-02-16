---
title: Descrevendo imagens - o de imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a funcionalidade de descrição de imagem da API de visão do computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312196"
---
# <a name="describe-images-with-human-readable-language"></a>Descrever as imagens com linguagem legível por humanos

Imagem digitalizada pode analisar uma imagem e gerar uma sentença legível por humanos, que descreve o respetivo conteúdo. O algoritmo, na verdade, retruns vários descrições com base em diferentes recursos do visual e cada descrição de recebe uma pontuação de confiança. O resultado final é uma lista de descrições ordenados do mais alto de confiança de mais baixa.

## <a name="image-description-example"></a>Exemplo de descrição de imagem

A resposta JSON seguinte ilustra o que o de imagem digitalizada devolve ao descrever a imagem de exemplo com base nos seus recursos do visual.

![Uma imagem simples de edifícios em Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [marcação de imagens](concept-tagging-images.md) e [categorizar imagens](concept-categorizing-images.md).
