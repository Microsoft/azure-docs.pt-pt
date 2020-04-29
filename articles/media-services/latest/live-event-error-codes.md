---
title: Códigos de erro de eventos ao vivo da Azure Media Services Microsoft Docs
description: Este artigo lista códigos de erro de eventos ao vivo.
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
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654584"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro de eventos ao vivo dos Media Services

As tabelas desta secção listam os códigos de erro [do Evento Ao Vivo.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejeitado

Ao subscrever os eventos da Grelha de [Eventos](https://docs.microsoft.com/azure/event-grid/) para um evento ao vivo, poderá ver um dos seguintes erros do evento [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Código do resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL incorreto |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificador não está presente na lista de permitir IP configurada |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificador RTMP não enviou o comando setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | O codec especificado não é suportado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |A informação sobre a descrição dos meios de comunicação social não foi recebida antes de os dados reais dos meios de comunicação social terem sido entregues.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A contagem de qualidades para o tipo de áudio ou vídeo excedeu o limite máximo permitido.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |A bitrate total de entrada num evento ao vivo ou serviço de canal excedeu o limite máximo permitido.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | A marca de tempo para vídeo ou áudio FLVTag é inválida a partir do codificador RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Os fluxos codificadores ingeridos com emoldurações excediam o máximo permitido de 30fps para codificar eventos/canais ao vivo.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Os fluxos codificadores ingeridos ultrapassaram as seguintes resoluções permitidas: 1920x1088 para codificação de eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo pass-through.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDesligado

Pode ver um dos seguintes erros do evento [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Código do resultado|Descrição|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|A sessão de RTMP foi cronometrada depois de estar inativa para o prazo permitido.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|A marca de tempo para vídeo ou áudio FLVTag é inválida a partir do codificador RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Codificador enviando dados muito rápido.|
|Códigos de erro desconhecidos|Estes códigos de erro podem ir desde o erro de memória até duplicar as entradas no mapa de hash.|


## <a name="see-also"></a>Consulte também

[Códigos de erro de Streaming Endpoint (Origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Stream live with Media Services](stream-live-tutorial-with-api.md)
