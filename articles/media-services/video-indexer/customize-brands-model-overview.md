---
title: Personalize um modelo de Marcas em Índice de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Marcas no Video Indexer e como personalizá-lo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586079"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalize um modelo de Marcas no Índice de Vídeo

O Video Indexer suporta a deteção de marcas a partir de texto sonoro e visual durante a indexação e reindexação de conteúdos de vídeo e áudio. A funcionalidade de deteção de marcas identifica menções de produtos, serviços e empresas sugeridas pela base de dados de marcas da Bing. Por exemplo, se a Microsoft for mencionada num conteúdo de vídeo ou áudio ou se aparecer em texto visual num vídeo, o Video Indexer deteta-o como uma marca no conteúdo. As marcas são desambiguadas de outros termos usando o contexto.

A deteção de marcas é útil em uma grande variedade de cenários de negócio, tais como arquivo de conteúdos e descoberta, publicidade contextual, análise de redes sociais, análise de competição de retalho, e muito mais. A deteção da marca Video Indexer permite-lhe indexar menções da marca em texto sonoro e visual, utilizando a base de dados de marcas Bing, bem como com a personalização, construindo um modelo de Marcas personalizadas para cada conta de Índice de Vídeo. A funcionalidade de modelo personalizado Brands permite-lhe selecionar se o Video Indexer irá ou não detetar marcas a partir da base de dados de marcas Bing, excluir certas marcas de serem detetadas (essencialmente criando uma lista de marcas não aprovadas), e incluir marcas que devam fazer parte do seu modelo que pode não estar na base de dados de marcas da Bing (essencialmente criando uma lista de marcas aprovadas). O modelo personalizado marcas que cria só estará disponível na conta em que criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Fora do exemplo de deteção de caixa

Na apresentação "Microsoft Build 2017 Day 2", a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes como texto visual e nunca como verbatim. O Video Indexer deteta com alta precisão que um termo é de facto uma marca baseada no contexto, cobrindo mais de 90k marcas fora da caixa, e constantemente atualizado. Às 02:25, o Video Indexer deteta a marca da fala e, novamente, às 02:40 do texto visual, que faz parte do logótipo das janelas.

![Visão geral das marcas](./media/content-model-customization/brands-overview.png)

Falar de janelas no contexto da construção não vai detetar a palavra "Windows" como marca, e o mesmo para Box, Apple, Fox, etc., com base em algoritmos avançados de Machine Learning que sabem desambiguar do contexto. A Deteção de Marcas funciona para todas as nossas línguas suportadas.  

## <a name="next-steps"></a>Passos seguintes

Para trazer as suas próprias marcas, confira estes tópicos:

[Personalize o modelo de Marcas usando APIs](customize-brands-model-with-api.md)

[Personalize o modelo de Marcas usando o site](customize-brands-model-with-website.md)
