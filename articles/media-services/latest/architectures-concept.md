---
title: Arquiteturas de Serviços de Mídia
titleSuffix: Azure Media Services
description: Este artigo descreve arquiteturas para serviços de mídia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: e00079190371b3ae0e318aaadd8cf724d9d339d0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512726"
---
# <a name="media-services-architectures"></a>Arquiteturas de Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Transmissão em fluxo em direto de multimédia digital

Uma solução de streaming ao vivo permite-lhe capturar vídeo em tempo real e transmiti-lo aos consumidores em tempo real, como entrevistas de streaming, conferências e eventos desportivos online. Nesta solução, o vídeo é capturado por uma câmara de vídeo e enviado para um ponto final de entrada de canal. O canal recebe o fluxo de entrada ao vivo e disponibiliza-o para streaming através de um ponto final de streaming para um navegador web ou uma aplicação móvel. O canal também fornece um ponto final de monitorização de pré-visualização para visualizar e validar o seu fluxo antes de ser processado e entregue. O canal também pode gravar e armazenar o conteúdo ingerido para ser transmitido mais tarde (vídeo-on-demand).

Esta solução baseia-se nos serviços geridos azure: Media Services e Content Delivery Network. Estes serviços funcionam num ambiente de alta disponibilidade, remendado e suportado, permitindo-lhe focar na sua solução em vez do ambiente em que se encontram.

Consulte [os meios digitais de streaming](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-live-stream) ao vivo no centro de Arquitetura Azure.

## <a name="instant-broadcasting-on-serverless-architecture"></a>Radiodifusão instantânea na arquitetura sem servidor

Simplificar comunicações e atualizações em tempo real usando código sem servidor. Esta arquitetura utiliza Funções Azure, Serviço SignalR, uma conta de armazenamento e uma base de dados SQL.

Consulte [a transmissão instantânea em Arquitetura Sem Servidor](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/instant-broadcasting-on-serverless-architecture) no centro de arquitetura Azure.

## <a name="video-on-demand-digital-media"></a>Multimédia digital de vídeo a pedido

Uma solução básica de vídeo-on-demand que lhe dá a capacidade de transmitir conteúdo sonoro gravado, como filmes, clips de notícias, segmentos de desporto, vídeos de treino e tutoriais de apoio ao cliente a qualquer dispositivo de ponta de vídeo, aplicação móvel ou navegador de ambiente de trabalho. Os ficheiros de vídeo são enviados para o armazenamento Azure Blob, codificados para um formato padrão multi-bitrate, e depois distribuídos através de todos os principais protocolos de streaming de bits adaptativos (HLS, MPEG-DASH, Smooth) para o cliente Azure Media Player.

Esta solução baseia-se nos serviços geridos Azure: Blob Storage, Content Delivery Network e Azure Media Player. Estes serviços funcionam num ambiente de alta disponibilidade, remendado e suportado, permitindo-lhe focar na sua solução em vez do ambiente em que se encontram.

Veja [os meios digitais de vídeo a pedido](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/digital-media-video) no centro de Arquitetura Azure.

## <a name="gridwich-media-processing-system"></a>Sistema de processamento multimédia do Gridwich

O sistema Gridwich incorpora as melhores práticas para o processamento e entrega de meios de comunicação em Azure. Embora o sistema gridwich seja específico para os meios de comunicação, o processamento de mensagens e o enquadramento de eventos podem aplicar-se a qualquer fluxo de trabalho de processamento de eventos apátridas.

Consulte [o sistema de processamento de mídia Gridwich](https://docs.microsoft.com/azure/architecture/reference-architectures/media-services/gridwich-architecture) no centro de arquitetura Azure.

## <a name="next-steps"></a>Passos seguintes

> [Descrição geral dos Serviços de Multimédia do Azure](media-services-overview.md)