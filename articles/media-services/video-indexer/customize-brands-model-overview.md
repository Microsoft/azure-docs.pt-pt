---
title: Personalizar um modelo de marcas no Video Indexer-Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de marcas no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838364"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizar um modelo de marcas no Video Indexer

O Video Indexer dá suporte à detecção de marcas de fala e texto visual durante a indexação e a reindexação de conteúdo de vídeo e áudio. O recurso de detecção de marca identifica as mencionações de produtos, serviços e empresas sugeridas pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em um conteúdo de vídeo ou áudio ou se aparecer no texto visual em um vídeo, Video Indexer o detectará como uma marca no conteúdo. As marcas são desambiguadas de outros termos usando o contexto.

A detecção de marcas é útil em uma ampla variedade de cenários de negócios, como conteúdo de arquivamento e descoberta, publicidade contextual, análise de mídia social, análise de competição de varejo e muito mais. Video Indexer detecção de marca permite que você indexe as mençãos de marca em fala e texto visual, usando o banco de dados de marcas do Bing, bem como a personalização, criando um modelo de marcas personalizadas para cada conta de Video Indexer. O recurso de modelo de marcas personalizadas permite que você selecione se Video Indexer detectará marcas do banco de dados de marcas do Bing, excluirá a detecção de determinadas marcas (essencialmente criando uma lista de bloqueios de marcas) e incluirá marcas que devem ser parte do seu modelo que pode não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de permissões de marcas). O modelo de marcas personalizadas que você cria só estará disponível na conta em que você criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Exemplo de detecção pronta para uso

Na apresentação do [Microsoft Build 2017 dia 2](https://www.videoindexer.ai/media/ed6ede78ad/) , a marca "Microsoft Windows" aparece várias vezes. Às vezes, na transcrição, às vezes como texto visual e nunca como literalmente. O Video Indexer detecta com alta precisão que um termo é realmente uma marca com base no contexto, cobrindo marcas 90ks prontas para uso e atualizando constantemente. Em 02:25, Video Indexer detecta a marca da fala e, em seguida, novamente às 02:40 do texto visual, que faz parte do logotipo do Windows.

![Visão geral das marcas](./media/content-model-customization/brands-overview.png)

Falar sobre o Windows no contexto de construção não detectará a palavra "Windows" como uma marca, e o mesmo para Box, Apple, Fox, etc., com base em algoritmos de Machine Learning avançados que sabem como remover a ambiguidade do contexto. A detecção de marca funciona para todos os nossos idiomas com suporte. Clique aqui para obter [o vídeo e o índice da palestra completa do Microsoft Build 2017 dia 2](https://www.videoindexer.ai/media/ed6ede78ad/).

Para trazer suas próprias marcas, Confira as próximas etapas.

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo de marcas usando APIs](customize-brands-model-with-api.md)

[Personalizar o modelo de marcas usando o site](customize-brands-model-with-website.md)
