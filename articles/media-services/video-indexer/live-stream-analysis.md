---
title: Análise ao vivo usando índice de vídeo
titleSuffix: Azure Media Services
description: Este artigo mostra como realizar uma análise ao vivo usando o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74186003"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Análise ao vivo com Índice de Vídeo

Azure Media Services Video Indexer é um serviço Azure projetado para extrair informações profundas de ficheiros de vídeo e áudio offline. Isto é para analisar um dado ficheiro de media já criado com antecedência. No entanto, para alguns casos de uso é importante obter as informações dos meios de comunicação de um feed ao vivo o mais rápido possível para desbloquear casos operacionais e outros de uso pressionados a tempo. Por exemplo, esses metadados ricos num fluxo ao vivo poderiam ser utilizados pelos produtores de conteúdos para automatizar a produção televisiva.

Uma solução descrita neste artigo, permite que os clientes utilizem o Video Indexer em resoluções quase em tempo real em feeds ao vivo. O atraso na indexação pode ser tão baixo como quatro minutos usando esta solução, dependendo dos pedaços de dados indexados, da resolução de entrada, do tipo de conteúdo e do computação alimentado para este processo.

![Os metadados do Indexer de Vídeo no live stream](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1 - Jogador de amostra exibindo os metadados do Indexer de Vídeo no live stream*

A [solução de análise de fluxo](https://aka.ms/livestreamanalysis) em questão, utiliza funções Azure e duas Aplicações Lógicas para processar um programa ao vivo a partir de um canal ao vivo em Azure Media Services com Video Indexer e exibe o resultado com o Azure Media Player mostrando o fluxo quase em tempo real.

Em alto nível, é composto por dois passos principais. O primeiro passo corre a cada 60 segundos, e toma um subclip dos últimos 60 segundos jogados, cria um ativo a partir dele e indexa-o através do Índice de Vídeo. Em seguida, o segundo passo é chamado uma vez que a indexação está completa. Os insights capturados são processados, enviados para Azure Cosmos DB, e o subclip indexado é eliminado.

O leitor de amostras reproduz o live stream e obtém as ideias da Azure Cosmos DB, usando uma Função Azure dedicada. Exibe os metadados e as miniaturas em sincronização com o vídeo ao vivo.

![As duas aplicações lógicas que processam o live stream a cada minuto na nuvem](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2 - As duas aplicações lógicas que processam o live stream a cada minuto na nuvem.*

## <a name="step-by-step-guide"></a>Guia passo a passo 

O código completo e um guia passo a passo para implementar os resultados podem ser encontrados no [projeto GitHub para análise de meios ao vivo com Índice de Vídeo](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)
