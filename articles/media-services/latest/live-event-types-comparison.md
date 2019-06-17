---
title: Tipos de LiveEvent de serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo mostra uma tabela detalhada que comparam LiveEvent tipos.
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075010"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de evento em direto

Nos serviços de multimédia do Azure, um [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação em tempo real e pass-through. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela seguinte compara as funcionalidades dos tipos de evento em direto.

| Funcionalidade | Evento em direto de pass-through | Padrão ou Premium1080p de eventos em direto |
| --- | --- | --- |
| Velocidade de transmissão única entrada é codificada em múltiplas velocidades de transmissão na cloud |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 em 60 quadros por segundo) |1080p (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Máximas camadas de dados de saída| Mesmo que a entrada|Até 6 (consulte as predefinições do sistema abaixo)|
| Feed de largura de banda agregada máxima de contribuição|60 Mbps|N/A|
| Velocidade de transmissão máxima para uma única camada na contribuição |20 Mbps|20 Mbps|
| Suporte para faixas de áudio de vários idiomas|Sim|Não|
| Codecs de vídeo de entrada suportado |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída suportados|Mesmo que a entrada|H.264/AVC|
| Suportado vídeo bitová hloubka, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada suportadas|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída suportados|Mesmo que a entrada|AAC-LC|
| Resolução máxima de vídeo de vídeo de saída|Mesmo que a entrada|Standard - 720p, Premium1080p - 1080p|
| Taxa máxima de quadros de vídeo de entrada|60 quadros por segundo|Padrão ou Premium1080p - 30 quadros por segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|
| Tempo de execução máximo| 24 horas x 365 dias, em direto lineares | Até 24 horas|
| Capacidade de transmitir através de embedded 608 de legenda oculta CEA/708 legendagem de áudio de dados|Sim|Sim|
| Suporte para a inserção de slates|Não|Não|
| Suporte para ad sinalização através da API| Não|Não|
| Suporte para ad sinalização por meio de mensagens do SCTE 35 em banda|Sim|Sim|
| Capacidade de recuperar de paralisações breves no feed de contribuição|Sim|Parcial|
| Suporte para não-uniforme GOPs de entrada|Sim|Não – entrada deve ter resolvido duração GOP|
| Suporte para entrada de taxa de quadros de variável|Sim|Não – entrada deve ser corrigida taxa de quadros. Secundárias variações são pela tolerar, por exemplo, durante o plano de movimento elevada. Mas o feed de contribuição não é possível remover a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Auto-shutoff de evento em direto quando a introdução do feed é perdido|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e velocidades de transmissão contidas na saída do codificador em direto é determinado através da [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se utilizar um **padrão** live encoder (LiveEventEncodingType.Standard), em seguida, o *Default720p* configuração predefinida especifica um conjunto de pares de taxa de bits/resolução 6 descrito abaixo. Caso contrário, se utilizar um **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), em seguida, o *Default1080p* specifiesthe predefinida de saída do conjunto de pares de taxa de bits/resolução. 

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para Default720p

**Default720p** irá codificar o vídeo de entrada para as camadas de 6 seguintes.

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do Stream de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alta |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alta |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alta |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alta |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alta |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alta |Video_340x192_200kbps |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação em direto, abra um pedido de suporte através do Portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Confirme que há apenas uma camada a 720p e seis camadas no máximo. Também especifica que está a solicitar uma predefinição de um codificador em direto Standard.

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para Default1080p

**Default1080p** irá codificar o vídeo de entrada para as camadas de 6 seguintes.

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do Stream de saída |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alta |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Alta |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Alta |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Alta |Video_640x360_800kbps |
| 400 |480 |270 |30 |Alta |Video_480x270_400kbps |
| 200 |320 |180 |30 |Alta |Video_320x180_200kbps |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação em direto, abra um pedido de suporte através do Portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Certifique-se de que existe apenas uma camada em 1080p e no máximo, 6 camadas. Também especifica que está a solicitar uma predefinição de um codificador em direto de Premium1080p.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Stream de áudio de saída para Default720p e Default1080p

Para ambos *Default720p* e *Default1080p* configurações predefinidas, de áudio está codificado para estéreo AAC-LC em 128 kbps, taxa de 48 kHz de amostragem.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
