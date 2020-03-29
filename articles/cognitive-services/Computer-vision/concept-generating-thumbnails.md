---
title: Miniaturas cortadas inteligente - Visão computorizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a geração de miniaturas para imagens utilizando a API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945222"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Gerar miniaturas de corte inteligente com visão computacional

Uma miniatura é uma representação reduzida de uma imagem. As miniaturas são usadas para representar imagens e outros dados de uma forma mais económica e amigável. A Computer Vision API utiliza uma colheita inteligente, juntamente com a redimensionamento da imagem, para criar miniaturas intuitivas para uma determinada imagem.

O algoritmo de geração de miniaturas computer Vision funciona da seguinte forma:

1. Remova elementos distraídores da imagem e identifique a _área de interesse_&mdash;da área da imagem em que aparece o ou o objeto principal.
1. Corte a imagem com base na _área de interesse_identificada.
1. Mude a relação de aspeto para se ajustar às dimensões da miniatura alvo.

## <a name="area-of-interest"></a>Área de interesse

Ao fazer o upload de uma imagem, a API computer Vision analisa-a para determinar a *área de interesse*. Pode então usar esta região para determinar como cortar a imagem. No entanto, a operação de colheita corresponderá sempre à relação aspeto desejada se for especificada.

Você também pode obter as coordenadas de caixa de delimitação crua desta mesma *área de interesse,* chamando a **área OfInterest** API em vez disso. Em seguida, pode utilizar estas informações para modificar a imagem original como desejar.

## <a name="examples"></a>Exemplos

A miniatura gerada pode variar muito dependendo do que especifica para altura, largura e colheita inteligente, como mostra a imagem seguinte.

![Uma imagem de montanha ao lado de várias configurações de corte](./Images/thumbnail-demo.png)

A tabela que se segue ilustra miniaturas típicas geradas pela Computer Vision para as imagens de exemplo. As miniaturas foram geradas para uma altura e largura específicas do alvo de 50 pixels, com colheita inteligente ativada.

| Imagem | Miniatura |
|-------|-----------|
|![Montanha ao ar livre ao pôr-do-sol, com silhueta de uma pessoa](./Images/mountain_vista.png) | ![Miniatura da Montanha Exterior ao pôr-do-sol, com silhueta de uma pessoa](./Images/mountain_vista_thumbnail.png) |
|![Uma flor branca com um fundo verde](./Images/flower.png) | ![Vision Analyze Flower miniatura](./Images/flower_thumbnail.png) |
|![Uma mulher no telhado de um prédio de apartamentos](./Images/woman_roof.png) | ![miniatura de uma mulher no telhado de um prédio de apartamentos](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a marcação de imagens](concept-tagging-images.md) e [categorizando imagens.](concept-categorizing-images.md)
