---
title: Terminologia - Azure
description: Este artigo fornece uma visão geral do Live Video Analytics na terminologia IoT Edge.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 51fcc962f6546d727dbbc5e7ff62dc9c4e5677af
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261045"
---
# <a name="terminology"></a>Terminologia

Este artigo fornece uma visão geral da terminologia relacionada com o [Live Video Analytics no IoT Edge](overview.md).

## <a name="azure-media-services"></a>Serviços de Multimédia do Azure

A Azure Media Services é uma plataforma de mídia na nuvem que lhe permite construir soluções de media. Pode saber mais sobre isso na documentação do [Azure Media Services.](../latest/media-services-overview.md)

## <a name="asset"></a>Recurso

[O ativo](../latest/assets-concept.md) é uma entidade da Azure Media Services que mapeia para um recipiente blob na conta de armazenamento Azure que está anexada a uma conta de Serviços de Mídia. Todos os ficheiros associados a um ativo são armazenados como bolhas nesse contentor, enquanto os Serviços de Mídia detém os metadados (por exemplo, um nome, descrição, tempo de criação) associados ao ativo.

O Live Video Analytics no IoT Edge pode criar ativos e/ou adicionar dados aos ativos existentes. Isto permite os cenários de gravação e reprodução de vídeo contínua e baseada em eventos (com captura de vídeo no dispositivo de borda, gravação para Azure Media Services e reprodução através das capacidades de streaming do Azure Media Services).

## <a name="streaming"></a>Transmissão em Fluxo

Se viu vídeos num dispositivo móvel de serviços como netflix, YouTube e outros, já experimentou o streaming de vídeo. A reprodução começa logo após o toque de "play" (se tiver largura de banda suficiente), e pode procurar para trás e para a frente ao longo da linha do tempo do vídeo. Com o streaming, a ideia é entregar apenas a parte do vídeo que está a ser assistido, e deixar o espectador começar a reproduzir o vídeo enquanto os dados ainda estão a ser transferidos de um servidor para o cliente de reprodução. No contexto da Azure Media Services, o [streaming](https://en.wikipedia.org/wiki/Streaming_media) refere-se ao processo de entrega de meios da [Azure Media Services](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview) a um cliente de streaming (por exemplo, Azure Media Player). Pode utilizar o Azure Media Services para transmitir vídeos a clientes utilizando protocolos de streaming de meios de comunicação baseados em HTTP, padrão http, como [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) e [MPEG-DASH](https://dashif.org/about/). O HLS é suportado pelo Azure Media Player, e jogadores web como [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS,](https://videojs.com/) [Shaka Player da Google,](https://github.com/google/shaka-player)ou você pode renderizar de forma nativa em aplicações móveis com [o Exoplayer](https://github.com/google/ExoPlayer) do Android e a [Fundação AV](https://developer.apple.com/av-foundation/)do iOS. MPEG-DASH é igualmente suportado pela Azure Media Player, [encontre uma lista de clientes nesta página](https://dashif.org/clients/). 

Ao utilizar [gráficos de mídia](#media-graph)para gravar vídeos a um ativo nos Azure Media Services, pode utilizar a capacidade de streaming dos Media Services para entregar streams de vídeo em HLS e DASH. Pode saber mais sobre isso no artigo [de reprodução](video-playback-concept.md) de vídeo.

## <a name="recording"></a>Gravação

No contexto de um sistema de gestão de vídeo para câmaras de segurança, a gravação de vídeo refere-se ao processo de captura de vídeo das câmaras e armazenamento de um ficheiro (ou ficheiros) para posterior visualização através de aplicações móveis e de navegador. A gravação de vídeo pode ser categorizada em [gravação contínua](continuous-video-recording-concept.md) de vídeo e gravação de vídeo baseada [em eventos](event-based-video-recording-concept.md). Estas são explicadas mais detalhadamente na página do conceito [de gravação de Vídeo.](video-recording-concept.md)

## <a name="media-graph"></a>Gráfico de mídia

[O gráfico de](media-graph-concept.md) mídia permite definir de onde os meios de comunicação devem ser capturados, como deve ser processado e onde os resultados devem ser entregues. Permite-lhe definir um gráfico composto por fontes, processadores e nós de pia e, portanto, fornece a capacidade para você construir aplicações de análise de vídeo ao vivo. O gráfico de mídia é coberto em detalhe na página do conceito de gráfico de mídia.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) refere-se ao Protocolo de Streaming em Tempo Real. É um protocolo de nível de aplicação para controlo sobre a entrega de dados com propriedades em tempo real. O RTSP fornece um enquadramento extensível para permitir a entrega controlada e a pedido de dados em tempo real, como áudio e vídeo. 

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) refere-se a um Sistema de Gestão de Vídeo. Estes sistemas são utilizados para configurar e controlar câmaras de CCTV, capturar e gravar vídeos dos mesmos. Estes sistemas também fornecem aplicações de clientes para reproduzir o vídeo gravado

## <a name="next-steps"></a>Passos seguintes

[Gráficos de mídia](media-graph-concept.md)
