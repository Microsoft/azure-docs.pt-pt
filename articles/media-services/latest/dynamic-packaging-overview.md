---
title: Descrição geral de empacotamento dinâmico dos serviços de multimédia do Azure | Documentos da Microsoft
description: O tópico apresenta uma visão geral do empacotamento dinâmico, nos serviços de multimédia.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: juliako
ms.openlocfilehash: d3222b2a2c47d6c2db4ca890a2618e89891d9deb
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804829"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de multimédia do Microsoft Azure podem ser utilizados para disponibilizar formatos de muitos arquivos de origem do suporte de dados, suportes de dados em fluxo em formatos, e a proteção de conteúdo formata a uma variedade de tecnologias de cliente (por exemplo, iOS e XBOX). Estes clientes compreender protocolos diferentes, por exemplo iOS requer que um formato de HTTP Live Streaming (HLS) e o Xbox necessitam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de arquivos transmissão em fluxo uniforme de velocidade de transmissão adaptável que deve servir para clientes que compreender HLS, MPEG DASH ou transmissão em fluxo uniforme de ficheiros (ISO Base Media 14496-12), pode tirar partido de dinâmico Empacotamento. O empacotamento é agnóstico para a resolução de vídeo, SD/HD/UHD - 4K são suportados.

[Pontos finais de transmissão em fluxo](streaming-endpoint-concept.md) é o serviço de empacotamento dinâmico dos serviços de multimédia utilizadas para disponibilizar conteúdo multimédia para jogadores do cliente. Empacotamento dinâmico é uma funcionalidade que vem padrão em todos os de transmissão em fluxo pontos de extremidade (Standard ou Premium). Não existe nenhum extra custo associado esta funcionalidade em serviços de multimédia v3. Com o empacotamento dinâmico, tudo o que precisa é um elemento que contenha um conjunto de ficheiros MP4 de velocidade de transmissão adaptável com ficheiros de manifestos. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, receberá o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Nos serviços de multimédia, o empacotamento dinâmico é utilizado se são de transmissão em fluxo a pedido ou em direto.

O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Vídeo a pedido fluxo de trabalho comum

Segue-se um comuns dos serviços de multimédia de transmissão em fluxo fluxo de trabalho em que é utilizado o empacotamento dinâmico.

1. Carregar um ficheiro de entrada (chamado de um ficheiro de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista dos formatos suportados, consulte [formatos suportados pelo Media Encoder Standard](media-encoder-standard-formats.md).
2. Codificar o ficheiro de mezanino para os conjuntos H.264 MP4 de velocidade de transmissão adaptável.
3. Publique o elemento que contém a conjunto MP4 de velocidade de transmissão adaptável.
4. Crie os URLs que visam diferentes formatos (HLS, traço e transmissão em fluxo uniforme). O ponto final de transmissão em fluxo seria cuide-se de que serve o manifesto correto e os pedidos para todos esses formatos diferentes. Por exemplo:

 - HLS: `http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)`
 - Traço: `http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)`
 - Uniforme: `http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest`
 
## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo suportado pelo empacotamento dinâmico

Empacotamento dinâmico suporta ficheiros MP4, que contêm vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportado pelo empacotamento dinâmico

Suporta ficheiros MP4, que contêm áudio codificado com o empacotamento dinâmico [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (3 de AC avançada ou E AC3), ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR de DTS, DTS HD, HD DTS sem perdas).

> [!NOTE]
> Empacotamento dinâmico não suporta ficheiros que contêm [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) áudio (AC3) (é um codec herdado).

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar, transmitir vídeos em fluxo](stream-files-tutorial-with-api.md)

