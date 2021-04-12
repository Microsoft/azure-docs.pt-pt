---
title: Deteção facial - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados com a funcionalidade de deteção facial da API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3aa8f9057c3c9e3a141824a9d23e9be8d0bbe4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532640"
---
# <a name="face-detection-with-computer-vision"></a>Deteção facial com visão de computador

A Visão Computacional pode detetar rostos humanos dentro de uma imagem e gerar a idade, sexo e retângulo para cada rosto detetado. 

> [!NOTE]
> Esta funcionalidade também é oferecida pelo serviço Azure [Face.](../face/index.yml) Consulte esta alternativa para uma análise facial mais detalhada, incluindo identificação facial e deteção de poses. 

## <a name="face-detection-examples"></a>Exemplos de deteção de rosto

O exemplo a seguir demonstra a resposta JSON devolvida pela Visão De Computador para uma imagem que contém um único rosto humano.

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

O exemplo seguinte demonstra a resposta JSON devolvida para uma imagem que contém múltiplos rostos humanos.

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

A funcionalidade de deteção facial faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Faces` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"faces"` secção.

* [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
