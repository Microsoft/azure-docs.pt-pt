---
title: Deteção de esquema de cor - Visão Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a deteção do esquema de cores em imagens utilizando a API da Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244737"
---
# <a name="detect-color-schemes-in-images"></a>Detetar esquemas de cores em imagens

A Computer Vision analisa as cores numa imagem para fornecer três atributos diferentes: a cor dominante do primeiro plano, a cor dominante do fundo, e o conjunto de cores dominantes para a imagem como um todo. As cores devolvidas pertencem ao conjunto: preto, azul, castanho, cinza, verde, laranja, rosa, roxo, vermelho, teal, branco e amarelo. 

A Visão Computacional também extrai uma cor de sotaque, que representa a cor mais vibrante da imagem, com base numa combinação de cores dominantes e saturação. A cor do sotaque é devolvida como um código de cores HExadecimal HTML. 

A Computer Vision também devolve um valor booleano indicando se uma imagem é a preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de deteção de esquemas de cores

O exemplo que se segue ilustra a resposta JSON devolvida pela Computer Vision ao detetar o esquema de cores da imagem de exemplo. Neste caso, a imagem de exemplo não é uma imagem a preto e branco, mas o primeiro plano dominante e as cores de fundo são em preto, e as cores dominantes para a imagem como um todo são a preto e branco.

![Montanha ao ar livre ao pôr-do-sol, com silhueta de uma pessoa](./Images/mountain_vista.png)

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

A tabela seguinte mostra o primeiro plano devolvido, o fundo e as cores da imagem para cada imagem da amostra.

| Imagem | Cores dominantes |
|-------|-----------------|
|![Uma flor branca com um fundo verde](./Images/flower.png)| Primeiro plano: Preto<br/>Fundo: Branco<br/>Cores: Preto, Branco, Verde|
![Um comboio que atravessa uma estação](./Images/train_station.png) | Primeiro plano: Preto<br/>Fundo: Preto<br/>Cores: Preto |

### <a name="accent-color-examples"></a>Exemplos de cores de sotaque

 A tabela seguinte mostra a cor do sotaque devolvido, como um valor de cor HExadecimal HTML, para cada imagem de exemplo.

| Imagem | Cor de destaque |
|-------|--------------|
|![Uma pessoa em pé em uma rocha da montanha ao pôr do sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um fundo verde](./Images/flower.png) | #C6A205 |
|![Um comboio que atravessa uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de deteção de brancos & pretos

A tabela seguinte mostra a avaliação a preto e branco da Computer Vision nas imagens da amostra.

| Imagem | Preto & branco? |
|-------|----------------|
|![Uma imagem a preto e branco de edifícios em Manhattan](./Images/bw_buildings.png) | true |
|![Uma casa azul e o quintal da frente](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Utilizar a API

A função de deteção de esquemade cores faz parte da [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Você pode chamar isto de API através de um SDK nativo ou através de chamadas REST. Incluir `Color` no parâmetro de consulta **visualFeatures.** Em seguida, quando obtém a resposta completa da JSON, `"color"` basta analisar a corda para o conteúdo da secção.

* [Quickstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quickstart: Analise uma imagem (REST API)](./quickstarts/csharp-analyze.md)
