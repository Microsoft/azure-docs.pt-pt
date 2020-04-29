---
title: LiveEvent baixa latência nos Serviços De Mídia Azure [ Microsoft Docs
description: Este tópico dá uma visão geral das definições de baixa latência do LiveEvent e mostra como definir baixa latência.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78199653"
---
# <a name="live-event-low-latency-settings"></a>Definições de baixa latência do Evento Ao Vivo

Este artigo mostra como definir baixa latência num [Evento Ao Vivo.](https://docs.microsoft.com/rest/api/media/liveevents) Também discute resultados típicos que se vê ao utilizar as definições de latência baixa em vários jogadores. Os resultados variam em função da CDN e da latência da rede.

Para utilizar a nova funcionalidade **LowLatency,** desloque a **StreamOptionsFlag** para **LowLatency** no **LiveEvent**. Ao criar [liveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) para reprodução HLS, coloque [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) a 1. Uma vez que o fluxo esteja em funcionamento, pode utilizar o [Azure Media Player](https://ampdemo.azureedge.net/) (página de demonstração AMP), e definir as opções de reprodução para utilizar o "Perfil heurístico de baixa latência".

> [!NOTE]
> Atualmente, o LowLatency HeuristicProfile in Azure Media Player foi concebido para reproduzir streams no protocolo MPEG-DASH, `format=mdp-time-csf` `format=mdp-time-cmaf`com formato CSF ou CMAF (por exemplo, ou ). 

O exemplo a seguir .NET mostra como definir **LowLatency** no **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Veja o exemplo completo: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Latência de Eventos Ao Vivo

As tabelas seguintes mostram resultados típicos para a latência (quando a bandeira lowLatency está ativada) nos Serviços de Media, medido a partir do momento em que o feed de contribuição chega ao serviço até quando um espectador vê a reprodução no leitor. Para utilizar a latência baixa da melhor forma, deve afinar as definições do codificador até 1 segundo de comprimento "Grupo de Imagens" (GOP). Ao utilizar um comprimento GOP mais elevado, minimiza o consumo de largura de banda e reduz-se o bitrate na mesma taxa de fotogramas. É especialmente benéfico em vídeos com menos movimento.

### <a name="pass-through"></a>Pass-through 

||2s GOP baixa latência habilitada|1s GOP baixa latência habilitada|
|---|---|---|
|TRAÇO em AMP|10s|8s|
|HLS no jogador nativo iOS|14s|10s|

### <a name="live-encoding"></a>Live Encoding

||2s GOP baixa latência habilitada|1s GOP baixa latência habilitada|
|---|---|---|
|TRAÇO em AMP|14s|10s|
|HLS no jogador nativo iOS|18s|13s|

> [!NOTE]
> A latência de ponta a ponta pode variar consoante as condições da rede local ou introduzindo uma camada de cache CDN. Devia testar as suas configurações exatas.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

