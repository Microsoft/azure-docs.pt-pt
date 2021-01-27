---
title: Códigos de erro de evento ao vivo da Azure Media Services
description: Este artigo lista códigos de erro de evento ao vivo.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 310e70bc8c7ddbf3bcd37d4f0276fe04c2979e73
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897854"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro do Evento Ao Vivo dos Serviços De Media

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

As tabelas desta secção listam os códigos de erro [do Evento Vivo.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Ao subscrever os eventos [Da Grelha de Eventos](../../event-grid/index.yml) para um evento ao vivo, poderá ver um dos seguintes erros do evento [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Código do resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão incorreta |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificante não está presente na lista de autorizações IP configuradas |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificar RTMP não enviou o comando SetDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | O código especificado não é suportado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |A informação sobre a descrição dos meios de comunicação não foi recebida antes da entrega dos dados dos meios de comunicação social.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A contagem de qualidades para o tipo de áudio ou vídeo excedeu o limite máximo permitido.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |O valor total de entrada num evento ao vivo ou serviço de canais excedeu o limite máximo permitido.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O tempotad para vídeo ou áudio FLVTag é inválido do codificader RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Os fluxos ingeridos com framerates ultrapassaram os 30fps máximos permitidos para codificar eventos/canais ao vivo.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Os fluxos ingeridos de entrada ultrapassaram as seguintes resoluções permitidas: 1920x1088 para codificação de eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo.|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | O evento ao vivo recebeu uma grande quantidade de dados áudio ao mesmo tempo, ou uma grande quantidade de dados de vídeo sem qualquer frames chave. Desconectámos o codificar para lhe dar a oportunidade de voltar a tentar com os dados corretos. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDis ligados

Pode ver um dos seguintes erros do [evento LiveEventEncoderDis.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Código do resultado|Descrição|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|A sessão de RTMP foi cronometrada depois de ter ficado inativa por um limite de tempo permitido.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|O tempotad para vídeo ou áudio FLVTag é inválido do codificader RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Codificação enviando dados muito rápido.|
|Códigos de erro desconhecidos|Estes códigos de erro podem variar de erro de memória a entradas duplicadas no mapa de haxixe.|


## <a name="see-also"></a>Veja também

[Códigos de erro de streaming endpoint (origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Próximos passos

[Tutorial: Stream ao vivo com serviços de mídia](stream-live-tutorial-with-api.md)
