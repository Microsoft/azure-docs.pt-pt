---
title: Configurações de baixa latência liveEvent nos Serviços Azure Media
description: Este tópico dá uma visão geral das configurações de baixa latência do LiveEvent e mostra como definir a baixa latência.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 023b0f4d7f0367882e0a5bb2be89c485c18bc03c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897837"
---
# <a name="live-event-low-latency-settings"></a>Configurações de baixa latência do Evento Ao Vivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como definir a baixa latência num [Evento Ao Vivo](/rest/api/media/liveevents). Também discute resultados típicos que você vê ao usar as definições de baixa latência em vários jogadores. Os resultados variam em função da CDN e da latência da rede.

Para utilizar a nova função **LowLatency,** define o **StreamOptionsFlag** para **LowLatency** no **LiveEvent**. Ao criar [liveOutput](/rest/api/media/liveoutputs) para reprodução HLS, desagrega [o LiveOutput.Hls.fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) para 1. Uma vez que o stream está em funcionamento, pode utilizar o [Azure Media Player](https://ampdemo.azureedge.net/) (página de demonstração AMP) e definir as opções de reprodução para utilizar o "Perfil de Heurística de Baixa Latência".

> [!NOTE]
> Atualmente, o LowLatency HeuristicProfile in Azure Media Player foi concebido para reproduzir streams de volta no protocolo MPEG-DASH, com formato CSF ou CMAF (por exemplo, `format=mdp-time-csf` ou `format=mdp-time-cmaf` ). 

O exemplo .NET a seguir mostra como definir **a LowLatency** no **LiveEvent**:

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

As tabelas que se seguem mostram resultados típicos de latência (quando a bandeira de LowLatency está ativada) nos Serviços de Comunicação Social, medidos a partir do momento em que o feed de contribuição chega ao serviço até quando um espectador vê a reprodução no leitor. Para utilizar a baixa latência da melhor forma, deve sintonizar as definições do codificador até 1 segundo de comprimento "Group Of Pictures" (GOP). Ao utilizar um comprimento de GOP mais elevado, minimiza o consumo de largura de banda e reduz a bitrate com a mesma taxa de fotogramas. É especialmente benéfico em vídeos com menos movimento.

### <a name="pass-through"></a>Pass-through 

||2s GOP baixa latência habilitada|1s GOP baixa latência habilitada|
|---|---|---|
|**DASH em AMP**|10|8s|
|**HLS no jogador nativo do iOS**|14s|10|

### <a name="live-encoding"></a>Live Encoding

||2s GOP baixa latência habilitada|1s GOP baixa latência habilitada|
|---|---|---|
|**DASH em AMP**|14s|10|
|**HLS no jogador nativo do iOS**|18s|13s|

> [!NOTE]
> A latência de ponta a ponta pode variar dependendo das condições da rede local ou introduzindo uma camada de caching CDN. Deve testar as configurações exatas.

## <a name="next-steps"></a>Próximos passos

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
