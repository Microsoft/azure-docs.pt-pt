---
title: Personalize um modelo de Marcas em Índice de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Marcas no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: a1b2738e3be2e2b8a388ef80ca94764cdae99b08
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047360"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalize um modelo de Marcas no Índice de Vídeo

O Video Indexer suporta a deteção de marcas a partir de texto sonoro e visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção de marcas identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas da Bing. Por exemplo, se a Microsoft for mencionada num conteúdo de vídeo ou áudio ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo. As marcas são desambiguadas de outros termos usando o contexto.

A deteção de marcas é útil em uma grande variedade de cenários de negócio, tais como arquivo de conteúdos e descoberta, publicidade contextual, análise de redes sociais, análise de competição de retalho, e muito mais. A deteção da marca Video Indexer permite-lhe indexar menções da marca em texto sonoro e visual, utilizando a base de dados de marcas Bing, bem como com a personalização, construindo um modelo de Marcas personalizadas para cada conta de Índice de Vídeo. A funcionalidade de modelo personalizado Brands permite-lhe selecionar se o Video Indexer irá ou não detetar marcas da base de dados das marcas Bing, excluir certas marcas de serem detetadas (essencialmente criando uma lista negra de marcas), e incluir marcas que devam fazer parte do seu modelo que pode não estar na base de dados de marcas da Bing (essencialmente criando uma lista branca de marcas). O modelo personalizado marcas que cria só estará disponível na conta em que criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Fora do exemplo de deteção de caixa

Na apresentação do [Microsoft Build 2017 Day 2,](https://www.videoindexer.ai/media/ed6ede78ad/) a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes como texto visual e nunca como verbatim. O Video Indexer deteta com alta precisão que um termo é de facto uma marca baseada no contexto, cobrindo mais de 90k marcas fora da caixa, e constantemente atualizado. Às 02:25, o Video Indexer deteta a marca da fala e, novamente, às 02:40 do texto visual, que faz parte do logótipo das janelas.

![Visão geral das marcas](./media/content-model-customization/brands-overview.png)

Falar de janelas no contexto da construção não vai detetar a palavra "Windows" como marca, e o mesmo para Box, Apple, Fox, etc., com base em algoritmos avançados de Machine Learning que sabem desambiguar do contexto. A Deteção de Marcas funciona para todas as nossas línguas suportadas. Clique aqui para obter [o vídeo e o índice de keynote do Microsoft Build 2017.](https://www.videoindexer.ai/media/ed6ede78ad/)

Para trazer as suas próprias marcas, confira os próximos passos.

## <a name="next-steps"></a>Passos seguintes

[Personalize o modelo de Marcas usando APIs](customize-brands-model-with-api.md)

[Personalize o modelo de Marcas usando o site](customize-brands-model-with-website.md)
