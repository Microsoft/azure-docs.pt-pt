---
title: Detecção facial-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conheça os conceitos relacionados ao recurso de detecção facial do API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3675acf59c6889dd00c8f26089f509bbcd3c724e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945239"
---
# <a name="face-detection-with-computer-vision"></a>Detecção facial com Pesquisa Visual Computacional

Pesquisa Visual Computacional pode detectar faces humanas dentro de uma imagem e gerar a idade, o sexo e o retângulo para cada face detectada. 

> [!NOTE]
> Esse recurso também é oferecido pelo serviço de [face](/azure/cognitive-services/face/) do Azure. Consulte essa alternativa para análise facial mais detalhada, incluindo identificação de face e detecção de pose. 

## <a name="face-detection-examples"></a>Exemplos de detecção facial

O exemplo a seguir demonstra a resposta JSON retornada por Pesquisa Visual Computacional para uma imagem que contém uma única face humana.

![Análise de imagem de rosto de mulher num terraço](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

O exemplo a seguir demonstra a resposta JSON retornada para uma imagem que contém várias faces humanas.

![Visão de foto da família analisar análise](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Consulte a documentação de referência de [imagem de análise](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para saber mais sobre como usar o recurso de detecção facial.
