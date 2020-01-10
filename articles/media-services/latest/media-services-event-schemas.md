---
title: Esquemas da grade de eventos do Azure para eventos dos serviços de mídia
description: Descreve as propriedades que são fornecidas para eventos dos serviços de mídia com a grade de eventos do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 01/07/2020
ms.author: juliako
ms.openlocfilehash: b1c094689c7669f03d5355be7a77b1836c90974c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750867"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Esquemas da grade de eventos do Azure para eventos dos serviços de mídia

Este artigo fornece os esquemas e as propriedades dos eventos dos serviços de mídia.

Para obter uma lista de exemplos de scripts e tutoriais, consulte [fonte de eventos dos serviços de mídia](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Tipos de evento relacionados ao trabalho

Os serviços de mídia emitem os tipos de evento relacionados ao **trabalho** descritos abaixo. Há duas categorias para os eventos relacionados ao **trabalho** : "monitoramento de alterações de estado do trabalho" e "alterações no estado de saída do trabalho de monitoramento". 

Você pode se registrar para todos os eventos inscrevendo-se no evento JobStateChange. Ou, você pode assinar apenas eventos específicos (por exemplo, Estados finais, como JobErrored, JobFinished e JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorando alterações de estado do trabalho

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Obter um evento para todas as alterações de estado do trabalho. |
| Microsoft.Media.JobScheduled| Obter um evento quando o trabalho passar para o estado agendado. |
| Microsoft.Media.JobProcessing| Obter um evento quando o trabalho passar para o estado de processamento. |
| Microsoft.Media.JobCanceling| Obter um evento quando o trabalho estiver em transição para o estado de cancelamento. |
| Microsoft.Media.JobFinished| Obter um evento quando o trabalho passar para o estado concluído. Esse é um estado final que inclui saídas de trabalho.|
| Microsoft.Media.JobCanceled| Obter um evento quando o trabalho passar para o estado cancelado. Esse é um estado final que inclui saídas de trabalho.|
| Microsoft.Media.JobErrored| Obter um evento quando o trabalho passar para o estado de erro. Esse é um estado final que inclui saídas de trabalho.|

Consulte os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="monitoring-job-output-state-changes"></a>Alterações de estado de saída do trabalho de monitoramento

Um trabalho pode conter várias saídas de trabalho (se você tiver configurado a transformação para ter várias saídas de trabalho). Se você quiser acompanhar os detalhes da saída do trabalho individual, ouça um evento de alteração de saída do trabalho.

Cada **trabalho** será em um nível mais alto do que **JobOutput**, assim os eventos de saída de trabalho serão acionados dentro de um trabalho correspondente. 

As mensagens de erro no `JobFinished`, `JobCanceled`, `JobError` gerar os resultados agregados para cada saída de trabalho – quando todos eles forem concluídos. Enquanto que os eventos de saída do trabalho são acionados conforme cada tarefa é concluída. Por exemplo, se você tiver uma saída de codificação, seguida por uma saída de análise de vídeo, você obterá dois eventos acionando como eventos de saída de trabalho antes do evento JobFinished final ser acionado com os dados agregados.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Obter um evento para todas as alterações de estado de saída do trabalho. |
| Microsoft.Media.JobOutputScheduled| Obter um evento quando a saída do trabalho passar para o estado agendado. |
| Microsoft.Media.JobOutputProcessing| Obter um evento quando a saída do trabalho fizer a transição para o estado de processamento. |
| Microsoft.Media.JobOutputCanceling| Obter um evento quando a saída do trabalho fizer a transição para o estado de cancelamento.|
| Microsoft.Media.JobOutputFinished| Obtenha um evento quando a saída do trabalho passar para o estado concluído.|
| Microsoft.Media.JobOutputCanceled| Obter um evento quando a saída do trabalho passar para o estado cancelado.|
| Microsoft.Media.JobOutputErrored| Obter um evento quando a saída do trabalho passar para o estado de erro.|

Consulte os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="monitoring-job-output-progress"></a>Progresso da saída do trabalho de monitoramento

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Esse evento reflete o progresso do processamento do trabalho, de 0% a 100%. O serviço tentará enviar um evento se houvesse 5% ou mais de aumento no valor de progresso ou tiver sido mais de 30 segundos desde o último evento (pulsação). O valor de progresso não tem garantia de iniciar em 0% ou alcançar 100%, nem é garantido aumentar em uma taxa constante ao longo do tempo. Esse evento não deve ser usado para determinar que o processamento foi concluído – em vez disso, você deve usar os eventos de alteração de estado.|

Consulte os [exemplos de esquema](#event-schema-examples) a seguir.

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Os serviços de mídia também emite os tipos de eventos **ao vivo** descritos abaixo. Há duas categorias para os eventos **ao vivo** : eventos de nível de fluxo e eventos de nível de rastreamento. 

### <a name="stream-level-events"></a>Eventos de nível de fluxo

Os eventos de nível de fluxo são gerados por transmissão ou conexão. Cada evento tem um parâmetro `StreamId` que identifica a conexão ou o fluxo. Cada fluxo ou conexão tem uma ou mais faixas de tipos diferentes. Por exemplo, uma conexão de um codificador pode ter uma faixa de áudio e quatro faixas de vídeo. Os tipos de evento de fluxo são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de conexão do codificador foi rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | O codificador estabelece conexão com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDisconnected | Desconexões do codificador. |

Consulte os [exemplos de esquema](#event-schema-examples) a seguir.

### <a name="track-level-events"></a>Eventos de nível de trilha

Os eventos de nível de trilha são gerados por faixa. 

> [!NOTE]
> Todos os eventos de nível de trilha são gerados depois que um codificador ao vivo é conectado.

Os tipos de evento de nível de rastreamento são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | O servidor de mídia descarta a parte de dados porque é muito tarde ou tem um carimbo de data/hora sobreposto (o carimbo de data/hora da nova parte de dados é menor que a hora de término da parte de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe a primeira parte de dados de cada faixa no fluxo ou na conexão. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia detecta que os fluxos de áudio e vídeo estão fora de sincronia. Use como um aviso porque a experiência do usuário pode não ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia detecta que qualquer um dos dois fluxos de vídeo provenientes do codificador externo está fora de sincronia. Use como um aviso porque a experiência do usuário pode não ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando o evento ao vivo estiver em execução. Fornece Resumo de integridade de ingestão.<br/><br/>Depois que o codificador tiver sido inicialmente conectado, o evento de pulsação continuará a emitir a cada 20 segundos, independentemente de o codificador ainda estar conectado ou não. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia detecta a descontinuidade no roteiro de entrada. |

Consulte os [exemplos de esquema](#event-schema-examples) a seguir.

## <a name="event-schema-examples"></a>Exemplos de esquema de evento

### <a name="jobstatechange"></a>JobStateChange

O exemplo a seguir mostra o esquema do evento **JobStateChange** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| anteriorstate | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho sendo notificado nesse evento. Por exemplo, "agendado: o trabalho está pronto para iniciar" ou "concluído: o trabalho foi concluído".|

Onde o estado do trabalho pode ser um dos valores: *enfileirado*, *agendado*, *processamento*, *concluído*, *erro*, *cancelado* *, cancelando*

> [!NOTE]
> Na *fila* só estará presente na propriedade **previousState** , mas não na propriedade **State** .

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Para cada alteração de estado de trabalho não final (como JobScheduled, JobProcessing, JobCanceling), o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Para cada alteração de estado final do trabalho (como JobFinished, JobCanceled, JobErrored), o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| outputs | Matriz | Obtém as saídas do trabalho.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

O exemplo a seguir mostra o esquema do evento **JobOutputStateChange** :

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Para cada alteração de estado de JobOutput, o esquema de exemplo é semelhante ao seguinte:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

O esquema de exemplo é semelhante ao seguinte:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

O exemplo a seguir mostra o esquema do evento **LiveEventConnectionRejected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| ingestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde este fluxo está chegando. |
| resultCode | string | O motivo pelo qual a conexão foi rejeitada. Os códigos de resultado são listados na tabela a seguir. |

Os códigos de resultado são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão incorreta |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificador não está presente na lista de permissões IP configurada |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificador não enviou metadados sobre o fluxo. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Não há suporte para o codec especificado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Um fragmento foi recebido antes do recebimento e do cabeçalho para esse fluxo. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | O número de qualidades especificado excede o limite máximo permitido. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | A taxa de bits agregada excede o limite máximo permitido. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de vídeo ou áudio é inválido do codificador RTMP. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderConnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por fornecer essa ID na URL de ingestão. |
| ingestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde este fluxo está chegando. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderDisconnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| streamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| ingestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde este fluxo está chegando. |
| resultCode | string | O motivo para a desconexão do codificador. Pode ser uma desconexão normal ou de um erro. Os códigos de resultado são listados na tabela a seguir. |

Os códigos de resultado do erro são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | A sessão RTMP atingiu o tempo limite depois de ficar ociosa por um limite de tempo permitido. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de vídeo ou áudio é inválido do codificador RTMP. |
| MPE_CAPACITY_LIMIT_REACHED | Codificador enviando dados muito rápido. |
| Códigos de erro desconhecidos | Esses códigos de erro podem variar de erro de memória para duplicar entradas no mapa de hash. |

Os códigos de resultado de desconexão normais são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| S_OK | O codificador foi desconectado com êxito. |
| MPE_CLIENT_TERMINATED_SESSION | Codificador desconectado (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificador desconectado (FMP4). |
| MPI_REST_API_CHANNEL_RESET | O comando de redefinição de canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | Comando de parada do canal recebido. |
| MPI_REST_API_CHANNEL_STOP | Manutenção do canal em andamento. |
| MPI_STREAM_HIT_EOF | O fluxo de EOF é enviado pelo codificador. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingDataChunkDropped** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo da faixa (áudio/vídeo). |
| trackName | string | Nome da faixa. |
| bitrate | número inteiro | Taxa de bits da faixa. |
| carimbo de data/hora | string | Carimbo de data/hora da parte de dados eliminada. |
| timescale | string | Escala de time-hora. |
| resultCode | string | Motivo do descarte da parte de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamReceived** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo da faixa (áudio/vídeo). |
| trackName | string | Nome da faixa (fornecida pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate* ). |
| bitrate | número inteiro | Taxa de bits da faixa. |
| ingestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| encoderIp | string  | IP do codificador. |
| encoderPort | string | Porta do codificador de onde este fluxo está chegando. |
| carimbo de data/hora | string | Primeiro carimbo de data/hora da parte de dados recebida. |
| timescale | string | Escala temporal em que o carimbo de data/hora é representado. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| minLastTimestamp | string | Mínimo dos últimos carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMinLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com o último carimbo de data/hora mínimo. |
| maxLastTimestamp | string | Máximo de todos os carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMaxLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com o último carimbo de data/hora máximo. |
| timescaleOfMinLastTimestamp| string | Obtém a escala de caso em que "MinLastTimestamp" é representado.|
| timescaleOfMaxLastTimestamp| string | Obtém a escala de caso em que "MaxLastTimestamp" é representado.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingVideoStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| firstTimestamp | string | Carimbo de data/hora recebido para um dos níveis de roteiros/qualidade do tipo vídeo. |
| firstDuration | string | Duração da parte de dados com o primeiro carimbo de data/hora. |
| secondTimestamp | string  | Carimbo de data/hora recebido para algum outro nível de faixa/qualidade do vídeo de tipo. |
| secondDuration | string | Duração da parte de dados com o segundo carimbo de data/hora. |
| timescale | string | Escala de tempo de carimbos de data/hora e duração.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo a seguir mostra o esquema do evento **LiveEventIngestHeartbeat** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo da faixa (áudio/vídeo). |
| trackName | string | Nome da faixa (fornecida pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate* ). |
| bitrate | número inteiro | Taxa de bits da faixa. |
| incomingBitrate | número inteiro | Taxa de bits calculada com base em partes de dados provenientes do codificador. |
| lastTimestamp | string | Carimbo de data/hora mais recente recebido para uma faixa nos últimos 20 segundos. |
| timescale | string | Escala de time-hora em que os carimbos de lista são expressos. |
| overlapCount | número inteiro | Número de partes de dados com carimbos de data/hora sobrepostos nos últimos 20 segundos. |
| discontinuityCount | número inteiro | Número de descontinuidades observados nos últimos 20 segundos. |
| nonIncreasingCount | número inteiro | O número de partes de dados com carimbos de data/hora no passado foi recebido nos últimos 20 segundos. |
| unexpectedBitrate | booleano | Se as taxas de bits esperadas e reais diferirem mais do que o limite permitido nos últimos 20 segundos. É verdadeiro se e somente se, incomingBitrate > = 2 * taxa de bits ou incomingBitrate < = taxa de bits/2 ou IncomingBitrate = 0. |
| state | string | Estado do evento ao vivo. |
| healthy | booleano | Indica se a ingestão é íntegra com base nas contagens e nos sinalizadores. Íntegro é verdadeiro se overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo a seguir mostra o esquema do evento **LiveEventTrackDiscontinuityDetected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| trackType | string | Tipo da faixa (áudio/vídeo). |
| trackName | string | Nome da faixa (fornecida pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate* ). |
| bitrate | número inteiro | Taxa de bits da faixa. |
| previousTimestamp | string | Carimbo de data/hora do fragmento anterior. |
| newTimestamp | string | Carimbo de data/hora do fragmento atual. |
| discontinuityGap | string | Lacuna entre dois carimbos de data/hora. |
| timescale | string | Escala de tempo na qual o carimbo de data e hora e a lacuna de descontinuidade são representados |

### <a name="common-event-properties"></a>Propriedades comuns de evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | O tópico EventGrid. Essa propriedade tem a ID de recurso para a conta dos serviços de mídia. |
| subject | string | O caminho do recurso para o canal dos serviços de mídia na conta dos serviços de mídia. Concatenar o tópico e o assunto fornece a ID do recurso para o trabalho. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. Por exemplo, "Microsoft. Media. JobStateChange". |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento dos serviços de mídia. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

## <a name="next-steps"></a>Passos seguintes

[Registrar-se para eventos de alteração de estado do trabalho](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Ver também

- [SDK do .NET do EventGrid que inclui eventos do serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições dos eventos dos serviços de mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
