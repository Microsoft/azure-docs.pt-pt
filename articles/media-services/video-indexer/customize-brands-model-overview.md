---
title: Personalizar um modelo de marcas no indexador de vídeo - Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma descrição geral sobre o que é um modelo de marcas no indexador de vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 15f4b8b6d4a27e721dfcaed8b9dd700e87e3a257
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872884"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizar um modelo de marcas no Video Indexer

O Video Indexer suporta a deteção de marca de voz e texto visual durante a indexação e a reindexação de conteúdo de áudio e vídeo. A funcionalidade de deteção de marca identifica menções de produtos, serviços, e as empresas sugerido pela base de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionado num conteúdo de vídeo ou áudio, ou se ele aparece no texto visual num vídeo, o indexador de vídeos Deteta-o como uma marca no conteúdo. As marcas são a ambigüidade removidas a partir de outros termos usando contexto.

Deteção de marca é útil numa grande variedade de cenários de negócios, como arquivo de conteúdo e deteção, publicidade contextual, análise de redes sociais, varejo competir analysis e muitos mais. Deteção de marca de indexador vídeo permite-lhe menções de marca de índice em voz e texto visual, utilizando a base de dados de marcas do Bing, bem como com a personalização com a criação de um modelo de marcas personalizado para cada conta do Video Indexer. O recurso de modelo de marcas personalizado permite-lhe selecionar se ou não Video Indexer detectará marcas do Bing marcas banco de dados, excluir determinado marcas famosas de que está a ser detetado (essencialmente, a criação de uma lista de preta das marcas) e incluem marcas que devem fazer parte da sua modelo que pode não estar na base de dados do marcas do Bing (criar, essencialmente, uma lista de permissões de marcas). O modelo de marcas personalizado que criar só estará disponível na conta em que criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Fora o exemplo de deteção de caixa

Na [dia 2 do Microsoft Build 2017](https://www.videoindexer.ai/media/ed6ede78ad/) apresentação, a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes, como visual texto e nunca textual. O Video Indexer Deteta com precisão elevada que uma condição é, na verdade, marca com base no contexto, que abrangem mais de 90k marcas de imediato e atualizam constantemente. No 02:25, Video Indexer Deteta a marca de voz e, em seguida, novamente em 40:02 de texto visual, que faz parte do logotipo do windows.

![Descrição geral de marcas](./media/content-model-customization/brands-overview.png)

Conversa sobre o windows no contexto de construção não irá detetar a palavra "Windows" como uma marca e as mesmas para a caixa, Apple, Fox, etc., com base em algoritmos de Machine Learning avançados que sabem como para eliminar a ambiguidade do contexto. A deteção de marca funciona para todos os idiomas suportados. Clique aqui para [completo vídeo de apresentação do dia 2 do Microsoft Build 2017 e índice](https://www.videoindexer.ai/media/ed6ede78ad/).

Para colocar as suas próprias marcas, confira os passos seguintes.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de marcas com APIs](customize-brands-model-with-api.md)

[Personalizar o modelo de marcas usando o Web site](customize-brands-model-with-website.md)
