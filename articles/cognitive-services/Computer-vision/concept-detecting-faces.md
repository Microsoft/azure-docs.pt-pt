---
title: Deteção facial - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a funcionalidade de deteção facial da API computer Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244706"
---
# <a name="face-detection-with-computer-vision"></a>Deteção facial com visão de computador

A Visão Computacional pode detetar rostos humanos dentro de uma imagem e gerar a idade, sexo e retângulo para cada rosto detetado. 

> [!NOTE]
> Esta funcionalidade também é oferecida pelo serviço Azure [Face.](/azure/cognitive-services/face/) Consulte esta alternativa para uma análise facial mais detalhada, incluindo identificação facial e deteção de poses. 

## <a name="face-detection-examples"></a>Exemplos de deteção facial

O exemplo que se segue demonstra a resposta da JSON devolvida pela Computer Vision para uma imagem que contenha um único rosto humano.

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

O exemplo seguinte demonstra a resposta da JSON devolvida para uma imagem que contém múltiplos rostos humanos.

![Visão analisar rosto de foto de família](./Images/family_photo_face.png)

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

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção facial faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar isto de API através de um SDK nativo ou através de chamadas REST. Incluir `Faces` no parâmetro de consulta **visualFeatures.** Em seguida, quando obtém a resposta completa da JSON, `"faces"` basta analisar a corda para o conteúdo da secção.

* [Quickstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)
