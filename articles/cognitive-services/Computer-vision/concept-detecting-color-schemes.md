---
title: Detecção de esquema de cores-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção do esquema de cores em imagens usando o API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945281"
---
# <a name="detect-color-schemes-in-images"></a>Detectar esquemas de cores em imagens

Pesquisa Visual Computacional analisa as cores em uma imagem para fornecer três atributos diferentes: a cor de primeiro plano dominante, a cor de fundo dominante e o conjunto de cores dominantes para a imagem como um todo. As cores retornadas pertencem ao conjunto: preto, azul, marrom, cinza, verde, laranja, rosa, roxo, vermelho, azul-azulado, branco e amarelo. 

Pesquisa Visual Computacional também extrai uma cor de destaque, que representa a cor mais vibrante da imagem, com base em uma combinação de cores e saturação dominantes. A cor de destaque é retornada como um código de cor hexadecimal HTML. 

Pesquisa Visual Computacional também retorna um valor booliano que indica se uma imagem está em preto e branco.

## <a name="color-scheme-detection-examples"></a>Exemplos de detecção de esquema de cores

O exemplo a seguir ilustra a resposta JSON retornada por Pesquisa Visual Computacional ao detectar o esquema de cores da imagem de exemplo. Nesse caso, a imagem de exemplo não é uma imagem em preto e branco, mas as cores dominantes de primeiro e segundo plano são pretas e as cores dominantes para a imagem como um todo são pretas e brancas.

![Montanhas de ar no pôr do sol, com a silhueta de uma pessoa](./Images/mountain_vista.png)

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

A tabela a seguir mostra as cores de primeiro plano, plano de fundo e imagem retornadas para cada imagem de exemplo.

| Image | Cores dominantes |
|-------|-----------------|
|![Uma flor branca com um plano de fundo verde](./Images/flower.png)| Frente Preto<br/>Seguindo Branco<br/>Cores Preto, branco, verde|
![Um trem em execução por meio de uma estação](./Images/train_station.png) | Frente Preto<br/>Seguindo Preto<br/>Cores Preto |

### <a name="accent-color-examples"></a>Exemplos de cor de destaque

 A tabela a seguir mostra a cor de destaque retornada, como um valor de cor HTML hexadecimal, para cada imagem de exemplo.

| Image | Cor de destaque |
|-------|--------------|
|![Uma pessoa em uma pedra de montanha no pôr no sol](./Images/mountain_vista.png) | #BB6D10 |
|![Uma flor branca com um plano de fundo verde](./Images/flower.png) | #C6A205 |
|![Um trem em execução por meio de uma estação](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Exemplos de detecção de preto & branco

A tabela a seguir mostra a avaliação em preto e branco de Pesquisa Visual Computacional nas imagens de exemplo.

| Image | Preto & branco? |
|-------|----------------|
|![Uma imagem em preto e branco de edifícios no Manhattan](./Images/bw_buildings.png) | true |
|![Uma casa azul e o jardim frontal](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre a [detecção de tipos de imagem](concept-detecting-image-types.md).
