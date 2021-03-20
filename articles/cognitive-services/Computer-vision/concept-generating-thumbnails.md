---
title: Miniaturas cortadas inteligentemente - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a geração de miniaturas para imagens usando a API de Visão De Computador.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945222"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Gerando miniaturas inteligentes com visão de computador

Uma miniatura é uma representação de tamanho reduzido de uma imagem. As miniaturas são usadas para representar imagens e outros dados de uma forma mais económica e amiga do layout. A API de Visão Computacional utiliza a colheita inteligente, juntamente com o redimensionamento da imagem, para criar miniaturas intuitivas para uma determinada imagem.

O algoritmo de geração de miniaturas da Visão Computacional funciona da seguinte forma:

1. Remova os elementos distraídos da imagem e identifique a _área de interesse_ da área da imagem em que aparece &mdash; o(s) objeto principal.
1. Corte a imagem com base na _área de interesse_ identificada.
1. Altere o rácio de aspeto para se ajustar às dimensões das miniaturas do alvo.

## <a name="area-of-interest"></a>Área de interesse

Ao fazer o upload de uma imagem, a API da Visão De Computador analisa-a para determinar a *área de interesse*. Pode então usar esta região para determinar como cortar a imagem. No entanto, a operação de corte corresponderá sempre à relação de aspeto desejada se for especificada.

Você também pode obter as coordenadas de caixa de delimitação bruta desta mesma *área de interesse,* chamando a **áreaOfInterest** API em vez disso. Em seguida, pode utilizar estas informações para modificar a imagem original como desejar.

## <a name="examples"></a>Exemplos

A miniatura gerada pode variar muito dependendo do que especifica para a altura, largura e corte inteligente, como mostra a imagem seguinte.

![Uma imagem de montanha ao lado de várias configurações de corte](./Images/thumbnail-demo.png)

A tabela a seguir ilustra as miniaturas típicas geradas pela Visão de Computador para as imagens de exemplo. As miniaturas foram geradas para uma altura e largura específicas de 50 pixels, com a colheita inteligente ativada.

| Imagem | Miniatura |
|-------|-----------|
|![Montanha ao pôr do sol, com silhueta de uma pessoa](./Images/mountain_vista.png) | ![Miniatura da Montanha Exterior ao pôr-do-sol, com silhueta de uma pessoa](./Images/mountain_vista_thumbnail.png) |
|![Uma flor branca com um fundo verde](./Images/flower.png) | ![Visão analisar miniatura de flor](./Images/flower_thumbnail.png) |
|![Uma mulher no telhado de um prédio de apartamentos](./Images/woman_roof.png) | ![miniatura de uma mulher no telhado de um prédio de apartamentos](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre a marcação de imagens](concept-tagging-images.md) e [categorizando imagens.](concept-categorizing-images.md)
