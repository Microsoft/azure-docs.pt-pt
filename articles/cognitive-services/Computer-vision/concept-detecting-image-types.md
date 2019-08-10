---
title: Detecção de tipo de imagem-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao recurso de detecção de tipo de imagem do API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945215"
---
# <a name="detecting-image-types-with-computer-vision"></a>Detectando tipos de imagem com Pesquisa Visual Computacional

Com a API de [análise de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , pesquisa Visual computacional pode analisar o tipo de conteúdo de imagens, indicando se uma imagem é uma clip-art ou um desenho de linha.

## <a name="detecting-clip-art"></a>Detectando clip-art

Pesquisa Visual Computacional analisa uma imagem e classifica a probabilidade de a imagem ser Clip-Art em uma escala de 0 a 3, conforme descrito na tabela a seguir.

| Value | Significado |
|-------|---------|
| 0 | Não é ClipArt |
| 1 | Incerta |
| 2 | Normal-clip-art |
| 3 | Boa-clip-art |

### <a name="clip-art-detection-examples"></a>Exemplos de detecção de clip-art

As respostas JSON a seguir ilustram o que Pesquisa Visual Computacional retorna ao classificar a probabilidade de imagens de exemplo sendo clip-art.

![Uma imagem de clip-art de uma fatia de queijo](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Uma casa azul e o jardim frontal](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Detectando desenhos de linha

Pesquisa Visual Computacional analisa uma imagem e retorna um valor booliano que indica se a imagem é um desenho de linha.

### <a name="line-drawing-detection-examples"></a>Exemplos de detecção de desenho de linha

As respostas JSON a seguir ilustram o que Pesquisa Visual Computacional retorna ao indicar se as imagens de exemplo são desenhos de linha.

![Uma imagem de desenho de linha de um Lion](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Uma flor branca com um plano de fundo verde](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Consulte a documentação de referência de [imagem de análise](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para saber como detectar tipos de imagem.
