---
title: Personalize um modelo de Marcas em Indexer de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Marcas em Indexer de Vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73838364"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalize um modelo de Marcas em Indexer de Vídeo

O Indexer de vídeo suporta a deteção da marca a partir da fala e do texto visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção da marca identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas bing. Por exemplo, se a Microsoft for mencionada num conteúdo de vídeo ou áudio ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo. As marcas são desambiguadas de outros termos usando o contexto.

A deteção de marcas é útil em uma grande variedade de cenários de negócio, tais como arquivo de conteúdos e descoberta, publicidade contextual, análise de redes sociais, análise de concorrência a retalho, e muito mais. A deteção da marca Indexer de vídeo permite-lhe indexar menções de marca em texto sonoro e visual, utilizando a base de dados das marcas bing, bem como com a personalização, construindo um modelo de Marcas personalizadas para cada conta de Video Indexer. A funcionalidade de modelo de marcas personalizadas permite-lhe selecionar se o Video Indexer irá ou não detetar marcas na base de dados das marcas Bing, excluir certas marcas de serem detetadas (essencialmente criando uma lista negra de marcas), e incluir marcas que deverão fazer parte do seu modelo que podem não estar na base de dados de marcas bing (essencialmente criando uma lista branca de marcas). O modelo personalizado de Marcas que cria só estará disponível na conta em que criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Fora do exemplo de deteção da caixa

Na apresentação do [Microsoft Build 2017 Day 2,](https://www.videoindexer.ai/media/ed6ede78ad/) a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes como texto visual e nunca tão verbatim. O Indexer de Vídeo deteta com alta precisão que um termo é de facto marca baseada no contexto, cobrindo mais de 90mil marcas fora da caixa, e constantemente atualizado. Às 02:25, o Video Indexer deteta a marca a partir da fala e novamente às 02:40 a partir de texto visual, que faz parte do logótipo das janelas.

![Visão geral das marcas](./media/content-model-customization/brands-overview.png)

Falar de janelas no contexto da construção não vai detetar a palavra "Windows" como marca, e o mesmo para Box, Apple, Fox, etc., com base em algoritmos avançados de Machine Learning que sabem desambiguar do contexto. A Deteção de Marcas funciona para todas as nossas línguas apoiadas. Clique aqui para ver o vídeo e o índice de keynote do [Microsoft Build 2017 Day 2.](https://www.videoindexer.ai/media/ed6ede78ad/)

Para trazer as suas próprias marcas, confira os próximos passos.

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md)

[Personalize o modelo de Marcas usando o site](customize-brands-model-with-website.md)
