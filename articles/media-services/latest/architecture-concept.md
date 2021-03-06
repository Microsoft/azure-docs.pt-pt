---
title: Arquiteturas dos Serviços de Multimédia
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
ms.openlocfilehash: 4d14ab8b72e3e120e8ee8cc2be4ae29f20c32e87
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964335"
---
# <a name="media-services-architectures"></a>Arquiteturas dos Serviços de Multimédia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Transmissão em fluxo em direto de multimédia digital

Uma solução de streaming ao vivo permite-lhe capturar vídeo em tempo real e transmiti-lo aos consumidores em tempo real, como entrevistas de streaming, conferências e eventos desportivos online. Nesta solução, o vídeo é capturado por uma câmara de vídeo e enviado para um ponto final de entrada de canal. O canal recebe o fluxo de entrada ao vivo e disponibiliza-o para streaming através de um ponto final de streaming para um navegador web ou uma aplicação móvel. O canal também fornece um ponto final de monitorização de pré-visualização para visualizar e validar o seu fluxo antes de ser processado e entregue. O canal também pode gravar e armazenar o conteúdo ingerido para ser transmitido mais tarde (vídeo-on-demand).

Esta solução baseia-se nos serviços geridos azure: Media Services e Content Delivery Network. Estes serviços funcionam num ambiente de alta disponibilidade, remendado e suportado, permitindo-lhe focar na sua solução em vez do ambiente em que se encontram.

Consulte [os meios digitais de streaming](/azure/architecture/solution-ideas/articles/digital-media-live-stream) ao vivo no centro de Arquitetura Azure.

## <a name="video-on-demand-digital-media"></a>Multimédia digital de vídeo a pedido

Uma solução básica de vídeo-on-demand que lhe dá a capacidade de transmitir conteúdo sonoro gravado, como filmes, clips de notícias, segmentos de desporto, vídeos de treino e tutoriais de apoio ao cliente a qualquer dispositivo de ponta de vídeo, aplicação móvel ou navegador de ambiente de trabalho. Os ficheiros de vídeo são enviados para o armazenamento Azure Blob, codificados para um formato padrão multi-bitrate, e depois distribuídos através de todos os principais protocolos de streaming de bits adaptativos (HLS, MPEG-DASH, Smooth) para o cliente Azure Media Player.

Esta solução baseia-se nos serviços geridos Azure: Blob Storage, Content Delivery Network e Azure Media Player. Estes serviços funcionam num ambiente de alta disponibilidade, remendado e suportado, permitindo-lhe focar na sua solução em vez do ambiente em que se encontram.

Veja [os meios digitais de vídeo a pedido](/azure/architecture/solution-ideas/articles/digital-media-video) no centro de Arquitetura Azure.

## <a name="gridwich-media-processing-system"></a>Sistema de processamento multimédia do Gridwich

O sistema Gridwich demonstra as melhores práticas para o processamento e entrega de ativos dos meios de comunicação em Azure. Embora o sistema gridwich seja específico para os meios de comunicação, o processamento de mensagens e o enquadramento de eventos podem aplicar-se a qualquer fluxo de trabalho de processamento de eventos apátridas.

Consulte [o sistema de processamento de mídia Gridwich](/azure/architecture/reference-architectures/media-services/gridwich-architecture) no centro de arquitetura Azure.

## <a name="next-steps"></a>Passos seguintes

> [Descrição geral dos Serviços de Multimédia do Azure](media-services-overview.md)