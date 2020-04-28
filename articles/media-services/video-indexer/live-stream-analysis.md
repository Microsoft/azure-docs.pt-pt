---
title: Análise de fluxo ao vivo usando o Indexer de Vídeo
titleSuffix: Azure Media Services
description: Este artigo mostra como realizar uma análise de transmissão ao vivo usando o Indexer de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74186003"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Análise de transmissão ao vivo com Indexer de Vídeo

O Azure Media Services Video Indexer é um serviço Azure projetado para extrair insights profundos de ficheiros de vídeo e áudio offline. Isto é para analisar um determinado ficheiro mediático já criado antecipadamente. No entanto, para alguns casos de uso é importante obter os insights mediáticos de um feed ao vivo o mais rápido possível para desbloquear casos operacionais e outros casos de uso pressionados a tempo. Por exemplo, esses metadados ricos num live stream poderiam ser utilizados pelos produtores de conteúdo para automatizar a produção televisiva.

Uma solução descrita neste artigo, permite que os clientes utilizem o Video Indexer em resoluções quase em tempo real em feeds ao vivo. O atraso na indexação pode ser tão baixo como quatro minutos usando esta solução, dependendo dos pedaços de dados indexados, da resolução de entrada, do tipo de conteúdo e da computação alimentada para este processo.

![Os metadados do Indexer de Vídeo na transmissão em direto](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1 - Leitor de amostras que exibe os metadados do Indexer de Vídeo na transmissão em direto*

A [solução](https://aka.ms/livestreamanalysis) de análise de fluxo em mãos, utiliza funções Azure e duas Aplicações Lógicas para processar um programa ao vivo a partir de um canal ao vivo no Azure Media Services com o Video Indexer e exibe o resultado com o Azure Media Player a mostrar o fluxo quase em tempo real.

Em alto nível, é composto por dois passos principais. O primeiro passo corre a cada 60 segundos, e leva um subclip dos últimos 60 segundos jogados, cria um ativo a partir dele e indexa-o através do Indexer de Vídeo. Em seguida, o segundo passo é chamado uma vez que a indexação está completa. Os insights capturados são processados, enviados para o Azure Cosmos DB, e o subclip indexado é eliminado.

O leitor de amostras joga o live stream e obtém as informações do Azure Cosmos DB, usando uma Função Azure dedicada. Exibe os metadados e miniaturas em sincronização com o vídeo ao vivo.

![As duas aplicações lógicas que processam o live stream a cada minuto na nuvem](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2 – As duas aplicações lógicas que processam o live stream a cada minuto na nuvem.*

## <a name="step-by-step-guide"></a>Guia passo a passo 

O código completo e um guia passo a passo para implementar os resultados podem ser encontrados no [projeto GitHub para análise](https://aka.ms/livestreamanalysis)de mídia ao vivo com O Indexante de Vídeo . 

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)
