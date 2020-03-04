---
title: Tipos de LiveEvent dos Serviços De Mídia Azure Microsoft Docs
description: Na Azure Media Services, um evento ao vivo pode ser definido para uma *codificação de passagem* ou *ao vivo.* Este artigo mostra uma tabela detalhada que compara os tipos de Eventos Ao Vivo.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244648"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de eventos ao vivo

No Azure Media Services, um [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser definido para um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo bitrate). 

Estes artigos comparam as características dos tipos de eventos ao vivo.

## <a name="types-comparison"></a>Comparação de tipos 

A tabela seguinte compara as características dos tipos de Eventos Ao Vivo. Os tipos são definidos durante a criação utilizando [liveEventEncodingType:](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None** - Um codificador ao vivo no local envia um fluxo de bitrate múltiplo. Os streams ingeridos passam pelo Live Event sem qualquer processamento adicional. Também referido como um evento ao vivo pass-through.
* **LiveEventEncodingType.Standard** - Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e media Services cria vários streams bitrate. Se o feed de contribuição for de 720p ou resolução superior, o predefinido **Predefinido Predefinido Default720p** codificará um conjunto de 6 pares de resolução/bitrate (os detalhes seguem-se mais tarde no artigo).
* **LiveEventEncodingType.Premium1080p** - Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e media Services cria vários streams bitrate. O predefinido Predefinido Predefinido Default1080p especifica o conjunto de saída de pares de resolução/bitrate (os detalhes seguem-se mais tarde no artigo). 

| Funcionalidade | Evento ao Vivo pass-through | Evento Ao Vivo Standard ou Premium1080p |
| --- | --- | --- |
| A entrada única de bitrate é codificada em várias bitrates na nuvem |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096x2160 a 60 fotogramas/seg) |1080p (1920x1088 a 30 fotogramas/seg)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximas na saída| O mesmo que a entrada|Até 6 (ver Predefinições do Sistema abaixo)|
| Largura de banda agregada máxima do feed de contribuição|60 Mbps|N/D|
| Bitrate máximo para uma única camada na contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de linguagem|Sim|Não|
| Codificadores de vídeo de entrada suportados |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída suportados|O mesmo que a entrada|H.264/AVC|
| Profundidade, entrada e saída de bits de vídeo suportado|Até 10 bits, incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada suportado|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída suportados|O mesmo que a entrada|AAC-LC|
| Resolução de vídeo máxima do vídeo de saída|O mesmo que a entrada|Standard - 720p, Premium1080p - 1080p|
| Taxa máxima de fotogramas do vídeo de entrada|60 fotogramas/segundo|Standard ou Premium1080p - 30 fotogramas/segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Streaming Suave)|RTMP, MP4 fragmentado (Streaming Suave)|
| Preço|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video"|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video"|
| Tempo máximo de execução| 24 horas x 365 dias, linear ao vivo | 24 horas x 365 dias, linear ao vivo (pré-visualização)|
| Capacidade de passar através de dados de legendas CEA 608/708 incorporados|Sim|Sim|
| Capacidade de ligar transcrição ao vivo|Sim|Sim|
| Suporte para inserção de ardósias|Não|Não|
| Suporte à sinalização de anúncios via API| Não|Não|
| Suporte para sinalização de anúncios através de mensagens sCTE-35 em banda|Sim|Sim|
| Capacidade de recuperar de breves bancas na alimentação de contribuições|Sim|Parcial|
| Suporte para GOPs de entrada não uniformes|Sim|Não – a entrada deve ter duração fixa do GOP|
| Suporte para entrada de taxa de fotogramas variável|Sim|Não – a entrada deve ser fixada. Pequenas variações são toleradas, por exemplo, durante cenas de movimento elevado. Mas o feed de contribuição não pode baixar a taxa de fotogramas (por exemplo, para 15 fotogramas/segundo).|
| Encerramento automático do Live Event quando se perde o feed de entrada|Não|Após 12 horas, se não houver LiveOutput a funcionar|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e bitrates contidos na saída do codificador vivo são determinados pelo [nome predefinido](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se utilizar um codificador **standard** ao vivo (LiveEventEncodingType.Standard), então o predefinido *Predefinido Padrão720p* especifica um conjunto de 6 pares de resolução/bitrate descritos abaixo. Caso contrário, se utilizar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então o predefinido *Predefinido Padrão1080p* especifica o conjunto de saída de pares de resolução/bitrate.

> [!NOTE]
> Não é possível aplicar o predefinido Default1080p num Live Event se tiver sido configurado para codificação ao vivo Standard - terá um erro. Também terá um erro se tentar aplicar o predefinido Default720p a um codificador ao vivo Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para Default720p

Se o feed de contribuição for de 720p ou uma resolução superior, o predefinido **Predefinido Predefinido Predefinido Default720p** codificará o feed nas seguintes 6 camadas. Na tabela abaixo, bitrate está em kbps, MaxFPS representa que a taxa de fotogramas máxima permitida (em quadros/segundo), Perfil representa o Perfil H.264 utilizado.

| Velocidade de transmissão | Pulsação | Altura | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alta |
| 2200 |960 |540 |30 |Alta |
| 1350 |704 |396 |30 |Alta |
| 850 |512 |288 |30 |Alta |
| 550 |384 |216 |30 |Alta |
| 200 |340 |192 |30 |Alta |

> [!NOTE]
> Se precisar personalizar o predefinição de codificação ao vivo, por favor abra um bilhete de apoio via Portal Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Confirme que há apenas uma camada a 720p e seis camadas no máximo. Também especifique que está a solicitar um predefinido para um codificador standard ao vivo.
> Os valores específicos dos bitrates e resoluções podem ser ajustados ao longo do tempo

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para Default1080p

Se o feed de contribuição for de resolução de 1080p, o predefinido **Predefinido Default1080p** codificará o feed nas seguintes 6 camadas.

| Velocidade de transmissão | Pulsação | Altura | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alta |
| 3000 |1280 |720 |30 |Alta |
| 1600 |960 |540 |30 |Alta |
| 800 |640 |360 |30 |Alta |
| 400 |480 |270 |30 |Alta |
| 200 |320 |180 |30 |Alta |

> [!NOTE]
> Se precisar personalizar o predefinição de codificação ao vivo, por favor abra um bilhete de apoio via Portal Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Verifique se há apenas uma camada a 1080p e, no máximo, 6 camadas. Especifique também que está a solicitar um predefinido para um codificador ao vivo Premium1080p.
> Os valores específicos dos bitrates e resoluções podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Fluxo de áudio de saída para Default720p e Default1080p

Para predefinições *Predefinidas Default720p* e *Default1080p,* o áudio é codificado para a aparelhagem AAC-LC a 128 kbps. A taxa de amostragem segue a da faixa áudio no feed de contribuição.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas do codificador vivo

A secção anterior descreve as propriedades do codificador vivo que podem ser controladas explicitamente, através do predefinido - como o número de camadas, resoluções e bitrates. Esta secção clarifica as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Duração do grupo de imagens (GOP)

O codificador ao vivo segue a estrutura [gop](https://en.wikipedia.org/wiki/Group_of_pictures) do feed de contribuição - o que significa que as camadas de saída terão a mesma duração do GOP. Assim, recomenda-se que configure o codificador no local para produzir um feed de contribuição que tenha uma duração fixa do GOP (normalmente 2 segundos). Isto garantirá que os fluxos HLS e MPEG DASH de saída do serviço também têm durações fixas de GOP. Pequenas variações nas durações do GOP são suscetíveis de ser toleradas pela maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de fotogramas

O codificador ao vivo também segue as durações dos quadros de vídeo individuais no feed de contribuição - o que significa que as camadas de saída terão quadros com as mesmas durações. Assim, recomenda-se que configure o codificador no local para produzir um feed de contribuição que tenha uma taxa fixa de fotogramas (no máximo 30 fotogramas/segundo). Isto garantirá que os fluxos HLS e MPEG DASH de saída do serviço também têm durações fixas de taxas de fotogramas. Pequenas variações nas taxas de fotogramas podem ser toleradas pela maioria dos dispositivos, mas não há garantiade que o codificador vivo produza uma saída que irá reproduzir corretamente. O seu codificador ao vivo no local não deve estar a deixar cair quadros (por exemplo. em condições de bateria baixas) ou variando a taxa de fotogramas de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução de camadas de alimentação e de produção de contribuições

O codificador ao vivo está configurado para evitar a conversão do feed de contribuição. Consequentemente, a resolução máxima das camadas de saída não excederá a do alimento para contribuição.

Por exemplo, se enviar um feed de contribuição a 720p para um Evento Ao Vivo configurado para codificação ao vivo Default1080p, a saída terá apenas 5 camadas, começando com 720p a 3Mbps, descendo para 1080p a 200 kbps. Ou se enviar um feed de contribuição a 360p para um Evento Ao Vivo configurado para codificação ao vivo Standard, a saída conterá 3 camadas (em resoluções de 288p, 216p e 192p). No caso degenerado, se enviar um feed de contribuição de, digamos, 160x90 pixels para um codificador standard ao vivo, a saída conterá uma camada a 160x90 resolução ao mesmo bitrate que a do feed de contribuição.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate de feed de contribuição e camadas de saída

O codificador ao vivo está configurado para honrar as definições de bitrate no preset, independentemente da bitrate do feed de contribuição. Como resultado, a bitrate das camadas de saída pode exceder a do alimento de contribuição. Por exemplo, se enviar um feed de contribuição numa resolução de 720p a 1 Mbps, as camadas de saída permanecerão as mesmas que na [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Passos seguintes

[Visão geral do streaming ao vivo](live-streaming-overview.md)
