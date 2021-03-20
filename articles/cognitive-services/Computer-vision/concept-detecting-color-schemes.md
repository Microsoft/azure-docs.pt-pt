---
title: Deteção de esquemas de cores - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a deteção do esquema de cores em imagens utilizando a API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e7774d2cd100931f92ff80066ebea4463c6f65c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532657"
---
# <a name="detect-color-schemes-in-images"></a>Detetar esquemas de cores em imagens

A Visão Computacional analisa as cores numa imagem para fornecer três atributos diferentes: a cor dominante do primeiro plano, a cor de fundo dominante e o conjunto de cores dominantes para a imagem como um todo. As cores devolvidas pertencem ao conjunto: preto, azul, castanho, cinzento, verde, laranja, rosa, roxo, vermelho, teal, branco e amarelo. 

A Visão Computacional também extrai uma cor de acento, que representa a cor mais vibrante da imagem, baseada numa combinação de cores dominantes e saturação. A cor do acento é devolvida como um código de cores HTML hexadécimal. 

A Visão computacional também devolve um valor boolean que indica se uma imagem é a preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de deteção de esquemas de cores

O exemplo a seguir ilustra a resposta JSON devolvida pela Visão Computacional ao detetar o esquema de cores da imagem do exemplo. Neste caso, a imagem do exemplo não é uma imagem a preto e branco, mas as cores dominantes em primeiro plano e fundo são pretas, e as cores dominantes para a imagem como um todo são a preto e branco.

![Montanha ao pôr do sol, com silhueta de uma pessoa](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Exemplos de cores dominantes

A tabela seguinte mostra o primeiro plano devolvido, o fundo e as cores de imagem para cada imagem de amostra.

| Imagem | Cores dominantes |
|-------|-----------------|
|![Uma flor branca com um fundo verde](./Images/flower.png)| Primeiro plano: Preto<br/>Fundo: Branco<br/>Cores: Preto, Branco, Verde|
![Um trem atravessando uma estação](./Images/train_station.png) | Primeiro plano: Preto<br/>Fundo: Preto<br/>Cores: Preto |

### <a name="accent-color-examples"></a>Exemplos de cor de acento

 A tabela seguinte mostra a cor de acento devolvido, como um valor de cor HTML hexadécimal, para cada imagem de exemplo.

| Imagem | Cor de destaque |
|-------|--------------|
|![Uma pessoa em pé em uma rocha de montanha ao pôr do sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um fundo verde](./Images/flower.png) | #C6A205 |
|![Um trem atravessando uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de deteção de branco & preto

A tabela seguinte mostra a avaliação a preto e branco da Visão de Computador nas imagens da amostra.

| Imagem | Preto & branco? |
|-------|----------------|
|![Uma imagem a preto e branco de edifícios em Manhattan](./Images/bw_buildings.png) | true |
|![Uma casa azul e o jardim da frente](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Utilizar a API

A funcionalidade de deteção de esquemas de cores faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Você pode chamar esta API através de um SDK nativo ou através de chamadas REST. Incluir `Color` no parâmetro de consulta **visualFeatures.** Em seguida, quando tiver a resposta JSON completa, basta analisar a corda para o conteúdo da `"color"` secção.

* [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
