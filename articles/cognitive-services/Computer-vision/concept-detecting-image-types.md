---
title: Deteção de tipo de imagem - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a funcionalidade de deteção de tipo de imagem da API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6d2ed00f3fc6f5b52a9a13a96f1e1659e30f02d5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532606"
---
# <a name="detecting-image-types-with-computer-vision"></a>Deteção de tipos de imagem com visão de computador

Com a [API de Imagem de Análise,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) a Visão Computacional pode analisar o tipo de conteúdo de imagens, indicando se uma imagem é clip art ou um desenho de linha.

## <a name="detecting-clip-art"></a>Deteção de clip-art

A Visão Computacional analisa uma imagem e classifica a probabilidade de a imagem ser clip art numa escala de 0 a 3, como descrito na tabela seguinte.

| Valor | Significado |
|-------|---------|
| 0 | Não é ClipArt |
| 1 | Ambíguo |
| 2 | Arte normal |
| 3 | Boa arte-clip |

### <a name="clip-art-detection-examples"></a>Exemplos de deteção de clipes

As seguintes respostas JSON ilustram o que a Visão Computacional retorna ao classificar a probabilidade de as imagens de exemplo serem clip art.

![Uma imagem de clip art de uma fatia de queijo](./Images/cheese_clipart.png)

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

![Uma casa azul e o jardim da frente](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Deteção de desenhos de linhas

A Visão Computacional analisa uma imagem e devolve um valor boolean que indica se a imagem é um desenho de linha.

### <a name="line-drawing-detection-examples"></a>Exemplos de deteção de desenho de linha

As seguintes respostas JSON ilustram o que a Visão Computacional retorna ao indicar se as imagens de exemplo são desenhos de linha.

![Uma imagem de desenho de linha de um leão](./Images/lion_drawing.png)

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

![Uma flor branca com um fundo verde](./Images/flower.png)

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

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção de tipo de imagem faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `ImageType` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"imageType"` secção.

* [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
