---
title: Miniaturas cortadas com inteligência inteligente-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à geração de miniaturas para imagens usando o API da Pesquisa Visual Computacional.
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
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945222"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Gerando miniaturas cortadas inteligente com Pesquisa Visual Computacional

Uma miniatura é uma representação de tamanho reduzido de uma imagem. As miniaturas são usadas para representar imagens e outros dados de maneira mais econômica e amigável para o layout. O API da Pesquisa Visual Computacional usa o corte inteligente, junto com o redimensionamento da imagem, para criar miniaturas intuitivas para uma determinada imagem.

O algoritmo de geração de miniatura Pesquisa Visual Computacional funciona da seguinte maneira:

1. Remova elementos de distração da imagem e identifique a _área de interesse_ &mdash;da área da imagem na qual os objetos principais são exibidos.
1. Cortar a imagem com base na _área de interesse_ identificada.
1. Altere a taxa de proporção para ajustar as dimensões de miniatura de destino.

## <a name="area-of-interest"></a>Área de interesse

Quando você carrega uma imagem, o API da Pesquisa Visual Computacional a analisa para determinar a *área de interesse*. Em seguida, ele pode usar essa região para determinar como cortar a imagem. A operação de corte, no entanto, sempre corresponderá à taxa de proporção desejada se uma for especificada.

Você também pode obter as coordenadas de caixa delimitadora bruta dessa mesma *área de interesse* chamando a API **areaOfInterest** em vez disso. Você pode usar essas informações para modificar a imagem original, no entanto, você deseja.

## <a name="examples"></a>Exemplos

A miniatura gerada pode variar muito dependendo do que você especificar para altura, largura e corte inteligente, conforme mostrado na imagem a seguir.

![Uma imagem de Mountain ao lado de várias configurações de corte](./Images/thumbnail-demo.png)

A tabela a seguir ilustra as miniaturas típicas geradas por Pesquisa Visual Computacional para as imagens de exemplo. As miniaturas foram geradas para uma altura e largura de destino especificadas de 50 pixels, com o corte inteligente habilitado.

| Image | Miniatura |
|-------|-----------|
|![Montanhas de ar no pôr do sol, com a silhueta de uma pessoa](./Images/mountain_vista.png) | ![Miniatura de montanhas de externa ao sol, com a silhueta de uma pessoa](./Images/mountain_vista_thumbnail.png) |
|![Uma flor branca com um plano de fundo verde](./Images/flower.png) | ![Miniatura da flor de análise de visão](./Images/flower_thumbnail.png) |
|![Uma mulher no telhado de uma construção de apartamento](./Images/woman_roof.png) | ![miniatura de uma mulher no telhado de um prédio de apartamento](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [Marcar imagens](concept-tagging-images.md) e [categorizar imagens](concept-categorizing-images.md).
