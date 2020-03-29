---
title: Deteção de tipo de imagem - Visão computorizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a funcionalidade de deteção do tipo de imagem da API da Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244567"
---
# <a name="detecting-image-types-with-computer-vision"></a>Deteção de tipos de imagem com visão de computador

Com a [API de imagem de análise,](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) a Computer Vision pode analisar o tipo de conteúdo das imagens, indicando se uma imagem é clip art ou um desenho de linha.

## <a name="detecting-clip-art"></a>Deteção de clipart

A Computer Vision analisa uma imagem e classifica a probabilidade de a imagem ser clip art numa escala de 0 a 3, como descrito na tabela seguinte.

| Valor | Significado |
|-------|---------|
| 0 | Não é ClipArt |
| 1 | Ambíguo |
| 2 | Clip-art normal |
| 3 | Boa clip-art |

### <a name="clip-art-detection-examples"></a>Exemplos de deteção de clipart

As seguintes respostas da JSON ilustram o que a Computer Vision retorna ao classificar a probabilidade de as imagens de exemplo serem clip art.

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

![Uma casa azul e o quintal da frente](./Images/house_yard.png)

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

A Computer Vision analisa uma imagem e devolve um valor booleano indicando se a imagem é um desenho de linha.

### <a name="line-drawing-detection-examples"></a>Exemplos de deteção de desenho de linha

As seguintes respostas da JSON ilustram o que a Visão de Computador retorna ao indicar se as imagens de exemplo são desenhos de linha.

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

A funcionalidade de deteção do tipo de imagem faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar isto de API através de um SDK nativo ou através de chamadas REST. Incluir `ImageType` no parâmetro de consulta **visualFeatures.** Em seguida, quando obtém a resposta completa da JSON, `"imageType"` basta analisar a corda para o conteúdo da secção.

* [Quickstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)
