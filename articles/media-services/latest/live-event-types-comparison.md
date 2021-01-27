---
title: Tipos de LiveEvent da Azure Media Services
description: Na Azure Media Services, um evento ao vivo pode ser definido para uma *codificação* de passagem ou *ao vivo.* Este artigo mostra uma tabela detalhada que compara tipos de Eventos Ao Vivo.
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
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897803"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de eventos ao vivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

No Azure Media Services, um  [Evento Ao Vivo](/rest/api/media/liveevents) pode ser definido para um passe *através* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificante ao vivo no local envia um único fluxo de bitrate). 

Estes artigos comparam características dos tipos de eventos ao vivo.

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara as características dos tipos de Eventos Ao Vivo. Os tipos são definidos durante a criação usando [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** - Um codificador ao vivo no local envia um fluxo bitrate múltiplo. Os fluxos ingeridos passam pelo Live Event sem qualquer processamento adicional. Também referido como um evento ao vivo.
* **LiveEventEncodingType.Standard** - Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e Media Services cria vários streams bitrate. Se o feed de contribuição for de 720p ou resolução superior, a predefinição Predefinição **720p** codificará um conjunto de 6 pares de resolução/bitrate (detalhes seguem mais tarde no artigo).
* **LiveEventEncodingType.Premium1080p** - Um codificador ao vivo no local envia um único fluxo bitrate para o Live Event e Media Services cria vários streams de bitrate. A predefinição predefinida 1080p especifica o conjunto de saída de pares de resolução/bitrate (detalhes seguem-se mais tarde no artigo). 

| Funcionalidade | Evento ao vivo de passagem | Standard ou Premium1080p Live Event |
| --- | --- | --- |
| A entrada bitrate única é codificada em vários bitrates na nuvem |No |Yes |
| Resolução máxima em vídeo para feed de contribuição |4K (4096x2160 a 60 fotogramas/seg) |1080p (1920x1088 a 30 fotogramas/seg)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximas na saída| O mesmo que a entrada|Até 6 (ver predefinições do sistema abaixo)|
| Largura de banda agregada máxima dos alimentos para a contribuição|60 Mbps|N/D|
| Bitrate máximo para uma única camada na contribuição |20 Mbps|20 Mbps|
| Suporte para múltiplas faixas de áudio de idioma|Yes|No|
| Codecs de vídeo de entrada suportados |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída suportados|O mesmo que a entrada|H.264/AVC|
| Profundidade, entrada e saída de bit de vídeo suportados|Até 10 bits incluindo HDR 10/HLG|8-bit|
| Codecs de áudio de entrada suportados|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída suportados|O mesmo que a entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|O mesmo que a entrada|Standard - 720p, Premium1080p - 1080p|
| Taxa máxima de fotogramas de vídeo de entrada|60 fotogramas/segundo|Standard ou Premium1080p - 30 fotogramas/segundo|
| Protocolos de entrada|RTMP, fragmentado-MP4 (Streaming Suave)|RTMP, fragmentado-MP4 (Streaming Suave)|
| Preço|Veja [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video"|Veja [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video"|
| Tempo máximo de execução| 24 hrs x 365 dias, ao vivo linear | 24 hrs x 365 dias, live linear (pré-visualização)|
| Capacidade de passar através de dados de legendas do CEA 608/708 incorporados|Yes|Yes|
| Capacidade de ligar a Transcrição ao Vivo|Yes|Yes|
| Suporte para inserção de ardósias|No|No|
| Suporte para sinalização de anúncios via API| No|No|
| Suporte para sinalização de anúncios através de mensagens scte-35 em banda|Yes|Yes|
| Capacidade de recuperar de breves bancas no feed de contribuições|Yes|Parcial|
| Suporte para GOPs de entrada não uniforme|Yes|Não – a entrada deve ter duração fixa do GOP|
| Suporte para entrada de taxa de fotogramas variáveis|Yes|Não – a entrada deve ser fixa. Pequenas variações são toleradas, por exemplo, durante cenas de movimento elevado. Mas o feed de contribuição não pode baixar a taxa de fotogramas (por exemplo, para 15 fotogramas/segundo).|
| Desligação automática do Live Event quando o feed de entrada é perdido|No|Depois de 12 horas, se não houver liveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e bitrates contidos na saída do codificadores vivos são determinados pelo [nome predefinido](/rest/api/media/liveevents/create#liveeventencoding). Se utilizar um codificador **standard** ao vivo (LiveEventEncodingType.Standard), então o predefinição *Preset 720p padrão* especifica um conjunto de 6 pares de resolução/bitrate descritos abaixo. Caso contrário, se utilizar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então o predefinidor *Predefinitivo 1080p* especifica o conjunto de saída dos pares de resolução/bitrate.

> [!NOTE]
> Não é possível aplicar a predefinição Predefinida 1080p a um Evento Ao Vivo se tiver sido configurada para codificação standard ao vivo - terá um erro. Também terá um erro se tentar aplicar a predefinição Predefinida 720p a um codificar ao vivo Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Streams de vídeo de saída para Default720p

Se o feed de contribuição for de 720p ou resolução superior, a predefinição Predefinição **Default720p** codificará o feed nas seguintes 6 camadas. Na tabela abaixo, o Bitrate está em kbps, o MaxFPS representa a taxa máxima permitida (em fotogramas/segundo), o Perfil representa o Perfil H.264 utilizado.

| Bitrate | Width | Height | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |
| 2200 |960 |540 |30 |Alto |
| 1350 |704 |396 |30 |Alto |
| 850 |512 |288 |30 |Alto |
| 550 |384 |216 |30 |Alto |
| 200 |340 |192 |30 |Alto |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação ao vivo, abra um bilhete de apoio através do Portal Azure. Deve especificar a tabela desejada de resolução de vídeo e bitrates. A personalização do bitrate de codificação de áudio não é suportada. Confirme que há apenas uma camada a 720p e seis camadas no máximo. Também especifique que está a solicitar uma predefinição.

### <a name="output-video-streams-for-default1080p"></a>Streams de vídeo de saída para Default1080p

Se o feed de contribuição for de resolução de 1080p, a predefinição Predefinição **1080p** codificará o feed nas seguintes 6 camadas.

| Bitrate | Width | Height | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alto |
| 3.000 |1280 |720 |30 |Alto |
| 1600 |960 |540 |30 |Alto |
| 800 |640 |360 |30 |Alto |
| 400 |480 |270 |30 |Alto |
| 200 |320 |180 |30 |Alto |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação ao vivo, abra um bilhete de apoio através do Portal Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Verifique se existe apenas uma camada a 1080p e, no máximo, 6 camadas. Além disso, especifique que está a solicitar uma predefinição para um codificadora premium1080p ao vivo. Os valores específicos dos bitrates e resoluções podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Fluxo de áudio de saída para Default720p e Default1080p

Tanto para predefinições *Default720p* *como Default1080p,* o áudio é codificado para estéreo AAC-LC a 128 kbps. A taxa de amostragem segue a da faixa áudio no feed de contribuição.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas do codificação ao vivo

A secção anterior descreve as propriedades do codificadora vivo que pode ser controlado explicitamente, através da predefinição - como o número de camadas, resoluções e bitrates. Esta secção esclarece as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Duração do grupo de imagens (GOP)

O codificadora ao vivo segue a estrutura [gop](https://en.wikipedia.org/wiki/Group_of_pictures) do feed de contribuição - o que significa que as camadas de saída terão a mesma duração de GOP. Assim, recomenda-se que configuure o codificadora no local para produzir um alimento para contribuição que tenha uma duração fixa de GOP (normalmente 2 segundos). Isto garantirá que os fluxos HLS e MPEG DASH de saída do serviço também têm durações de GOP fixas. Pequenas variações nas durações do GOP são suscetíveis de ser toleradas pela maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de fotogramas

O codificadora ao vivo também segue as durações dos quadros de vídeo individuais no feed de contribuição - o que significa que as camadas de saída terão quadros com as mesmas durações. Assim, recomenda-se que configufique o codificadora no local para produzir um alimento para contribuição que tenha uma taxa de fotogramas fixa (no máximo 30 fotogramas/segundo). Isto garantirá que os fluxos HLS e MPEG DASH de saída do serviço também têm durações fixas de taxas de fotogramas. Pequenas variações nas taxas de fotogramas podem ser toleradas pela maioria dos dispositivos, mas não há garantias de que o codificador vivo produza uma saída que irá jogar corretamente. O seu codificadora ao vivo no local não deve estar a deixar cair quadros (por exemplo. em condições de bateria baixas) ou variando a taxa de fotogramas de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução das camadas de alimentação e de saída de contribuições

O codificadora ao vivo está configurado para evitar a não transmissão do feed de contribuição. Como resultado, a resolução máxima das camadas de saída não excederá a do alimento para a contribuição.

Por exemplo, se enviar um feed de contribuição a 720p para um Evento Ao Vivo configurado para codificação ao vivo default1080p, a saída terá apenas 5 camadas, começando com 720p a 3Mbps, descendo para 1080p a 200 kbps. Ou se enviar um feed de contribuição a 360p para um Evento Ao Vivo configurado para codificação standard ao vivo, a saída conterá 3 camadas (em resoluções de 288p, 216p e 192p). No caso degenerado, se enviar um feed de contribuição de, digamos, 160x90 pixels para um codificader Standard live, a saída conterá uma camada a 160x90 resolução na mesma bitrate que a do feed de contribuição.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate de alimentos para animais de contribuição e camadas de saída

O codificante ao vivo está configurado para honrar as definições de bitrate na predefinição, independentemente do bitrate do feed de contribuição. Como resultado, o bitrate das camadas de saída pode exceder a do alimento para a contribuição. Por exemplo, se enviar um feed de contribuição com uma resolução de 720p a 1 Mbps, as camadas de saída permanecerão as mesmas que na [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Próximos passos

[Visão geral do streaming ao vivo](live-streaming-overview.md)
