---
title: Latência de LiveEvent nos serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico fornece uma visão geral de latência de LiveEvent e mostra como definir a baixa latência.
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 30cd77799837f9b1ef08a9c609e518fd679b9b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871427"
---
# <a name="live-event-latency-in-media-services"></a>Latência de evento em direto nos serviços de multimédia

Este artigo mostra como definir a baixa latência num [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents). Ele também aborda típicos resultados que vê ao usar as definições de baixa latência em diversos leitores. Os resultados variam com base na latência de rede e da CDN.

Utilizar a nova **LowLatency** conjunto de recursos, o **StreamOptionsFlag** para **LowLatency** sobre a **LiveEvent**. Ao criar [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) para reproduzir HLS, defina [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) como 1. Depois do fluxo está em execução, pode utilizar o [leitor de multimédia do Azure](https://ampdemo.azureedge.net/) (página de demonstração de AMP) e definir as opções de reprodução para utilizar o "baixa latência heurística perfil".

> [!NOTE]
> Atualmente, o LowLatency HeuristicProfile no leitor de multimédia do Azure foi concebido para reprodução de fluxos no protocolo DASH ou HLS com CMAF. Se estiver a filtrar dispositivos MacOS ou iOS através da HLS com TS (por exemplo, `format=m3u8-aapl` ou `format=m3u8-aapl-v3`), não deve utilizar esta definição, uma vez que AMP utiliza diretamente o leitor nativo fornecido pelo sistema operacional, neste caso.

O exemplo de .NET seguinte mostra como definir **LowLatency** sobre o **LiveEvent**:

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

## <a name="live-events-latency"></a>Latência de eventos em direto

As tabelas seguintes mostram os resultados típicos para latência (quando o sinalizador de LowLatency está ativado) nos serviços de multimédia, medido a partir do momento no feed de contribuição atinge o serviço para quando um visualizador vê a reprodução no leitor. Para utilizar a baixa latência ideal, deve ajustar as definições de codificador para baixo até 1 segundo comprimento de "Grupo de imagens" (GOP). Quando utiliza um comprimento de GOP superior, minimizar o consumo de largura de banda e reduzir a velocidade de transmissão na mesma taxa de quadros. É particularmente vantajoso em vídeos com menos movimento.

### <a name="pass-through"></a>Pass-through 

||2s GOP baixa latência ativada|1s GOP baixa latência ativada|
|---|---|---|
|TRAVESSÃO no AMP|10s|8s|
|HLS no player de iOS nativo|14s|10s|

### <a name="live-encoding"></a>Live Encoding

||2s GOP baixa latência ativada|1s GOP baixa latência ativada|
|---|---|---|
|TRAVESSÃO no AMP|14s|10s|
|HLS no player de iOS nativo|18s|13s|

> [!NOTE]
> A latência de ponto a ponto pode variar dependendo das condições de rede local ou com a introdução de uma camada de colocação em cache da CDN. Deve testar suas configurações exatas.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
- [Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)

