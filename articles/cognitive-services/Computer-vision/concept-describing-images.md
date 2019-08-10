---
title: Descrições de imagem-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao recurso de descrição da imagem do API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945269"
---
# <a name="describe-images-with-human-readable-language"></a>Descrever imagens com linguagem legível

Pesquisa Visual Computacional pode analisar uma imagem e gerar uma frase legível que descreve seu conteúdo. O algoritmo realmente retorna várias descrições com base em diferentes recursos visuais, e cada descrição recebe uma pontuação de confiança. A saída final é uma lista de descrições ordenadas da mais alta para a mais baixa confiança.

## <a name="image-description-example"></a>Exemplo de descrição da imagem

A resposta JSON a seguir ilustra o que Pesquisa Visual Computacional retorna ao descrever a imagem de exemplo com base em seus recursos visuais.

![Uma imagem em preto e branco de edifícios no Manhattan](./Images/bw_buildings.png)

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

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre como [Marcar imagens](concept-tagging-images.md) e [categorizar imagens](concept-categorizing-images.md).
