---
title: O Azure Event Grid esquemas para eventos de serviços de multimédia
description: Descreve as propriedades que são fornecidas para eventos de serviços de multimédia com o Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: f9fe689e6911c5e9497ee82132e8b70bd9aada7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322238"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>O Azure Event Grid esquemas para eventos de serviços de multimédia

Este artigo fornece os esquemas e as propriedades para eventos de serviços de multimédia.

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de eventos dos serviços de multimédia](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Tipos de eventos relacionados com a tarefa

Serviços de multimédia emite a **tarefa** relacionados com os tipos de eventos descritos abaixo. Existem duas categorias para o **tarefa** eventos relacionados com: "Altera de estado da tarefa de monitorização" e "Estado de saída de tarefa de monitorização é alterado". 

Pode registar para todos os eventos inscrevendo-se ao evento JobStateChange. Em alternativa, pode subscrever eventos específicos apenas (por exemplo, Estados finais como JobErrored, JobFinished e JobCanceled). 

### <a name="monitoring-job-state-changes"></a>Monitorização de alterações de estado de tarefa

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Obtém um evento para todas as alterações de estado da tarefa. |
| Microsoft.Media.JobScheduled| Obtenha um evento quando a tarefa faz a transição para o estado de agendada. |
| Microsoft.Media.JobProcessing| Obtenha um evento quando a tarefa faz a transição para o estado de processamento. |
| Microsoft.Media.JobCanceling| Obtenha um evento quando a tarefa faz a transição para o cancelamento de estado. |
| Microsoft.Media.JobFinished| Obtenha um evento quando a tarefa faz a transição para o estado concluído. Este é um estado final que inclui as saídas de tarefas.|
| Microsoft.Media.JobCanceled| Obtenha um evento quando a tarefa faz a transição para o estado cancelado. Este é um estado final que inclui as saídas de tarefas.|
| Microsoft.Media.JobErrored| Obtenha um evento quando a tarefa faz a transição para o estado de erro. Este é um estado final que inclui as saídas de tarefas.|

Ver [exemplos de esquema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-state-changes"></a>As alterações de estado da saída da tarefa de monitorização

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Obter um evento de saída da tarefa todas as que alterações de estado. |
| Microsoft.Media.JobOutputScheduled| Obtenha um evento quando as transições de estado agendada da saída da tarefa. |
| Microsoft.Media.JobOutputProcessing| Obtenha um evento quando as transições de estado de processamento da saída da tarefa. |
| Microsoft.Media.JobOutputCanceling| Obtenha um evento quando transições para cancelar o estado da saída da tarefa.|
| Microsoft.Media.JobOutputFinished| Obtém um evento quando terminar de transições de saída da tarefa de estado.|
| Microsoft.Media.JobOutputCanceled| Obtém um evento quando cancelada de transições de saída da tarefa de estado.|
| Microsoft.Media.JobOutputErrored| Obtenha um evento quando as transições de estado de erro da saída da tarefa.|

Ver [exemplos de esquema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-progress"></a>Progresso da saída da tarefa de monitorização

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Este evento reflete a tarefa de progresso, entre 0% a 100% do processamento. O serviço tenta enviar um evento se houve 5% ou maior aumento no valor de progresso ou tiver sido mais de 30 segundos desde o último evento (heartbeat). O valor de progresso não é garantido para começar a 0%, ou para chegar a 100%, nem é garantida para aumentar a uma taxa constante ao longo do tempo. Este evento não deve ser utilizado para determinar que o processamento foi concluído – deve usar os eventos de alteração de estado.|

Ver [exemplos de esquema](#event-schema-examples) que se seguem.

## <a name="live-event-types"></a>Tipos de evento em direto

Serviços de multimédia também emite a **Live** tipos de eventos descritos abaixo. Existem duas categorias para o **Live** eventos: ao nível do fluxo de eventos e eventos em nível de controle. 

### <a name="stream-level-events"></a>Eventos ao nível do Stream

Eventos em nível de Stream são emitidos por fluxo ou ligação. Cada evento tem um `StreamId` parâmetro identifica o ligação ou fluxo. Cada fluxo ou a ligação tem um ou mais roteiros de diferentes tipos. Por exemplo, uma ligação a partir de um codificador pode ter uma faixa de áudio e as faixas de vídeo de quatro. Os tipos de eventos de fluxo são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Tentativa de ligação do codificador é rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | Codificador estabelece ligação com o evento em direto. |
| Microsoft.Media.LiveEventEncoderDisconnected | Codificador desliga. |

Ver [exemplos de esquema](#event-schema-examples) que se seguem.

### <a name="track-level-events"></a>Eventos ao nível do Roteiro

Eventos de nível de controle são gerados por faixa. 

> [!NOTE]
> Todos os eventos de nível de controle são gerados depois de um codificador em direto está ligado.

Os tipos de eventos de nível de controle são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Servidor de mídia ignora os segmentos de dados porque este seja muito tarde ou um timestamp sobreposto (timestamp de novos segmentos de dados é inferior à hora de fim do segmento de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Servidor de suporte de dados recebe o primeiro segmento de dados para cada faixa no stream ou ligação. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Servidor de suporte de dados Deteta áudio e fluxos de vídeo não estão sincronizados. Utilizar como um aviso porque a experiência do usuário não poderá ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Servidor de suporte de dados Deteta qualquer um dos dois fluxos de vídeo vindo do codificador externa não estão sincronizadas. Utilizar como um aviso porque a experiência do usuário não poderá ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando está a executar o evento em direto. Fornece ingestão de resumo de estado de funcionamento.<br/><br/>Depois do codificador inicialmente estava conectado, o evento de heartbeat continua emitir cada seg 20 se o codificador ainda está ligado ou não. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Servidor de suporte de dados Deteta descontinuidade na faixa de entrada. |

Ver [exemplos de esquema](#event-schema-examples) que se seguem.

## <a name="event-schema-examples"></a>Exemplos de esquema de eventos

### <a name="jobstatechange"></a>JobStateChange

O exemplo seguinte mostra o esquema do **JobStateChange** eventos: 

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
| previousState | string | O estado da tarefa antes do evento. |
| state | string | O novo estado da tarefa a ser notificado neste evento. Por exemplo, "agendada: A tarefa está pronta para começar a"ou" concluído: A tarefa é concluída".|

Em que o estado da tarefa pode ser um dos valores: *Em fila*, *agendada*, *processamento*, *concluído*, *erro*, *cancelada*, *Cancelar*

> [!NOTE]
> *Em fila* apenas vai estar presente no **previousState** propriedade, mas não na **estado** propriedade.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Para cada não final tarefa alteração de estado (por exemplo, JobCanceling JobScheduled, JobProcessing,), o esquema de exemplo terá um aspeto semelhante ao seguinte:

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

Para cada alteração de estado final da tarefa (por exemplo, JobErrored JobFinished, JobCanceled,), o esquema de exemplo terá um aspeto semelhante ao seguinte:

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
| Saídas | Array | Obtém a tarefa de saídas.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

O exemplo seguinte mostra o esquema do **JobOutputStateChange** eventos:

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

Para cada alteração de estado de JobOutput, o esquema de exemplo terá um aspeto semelhante ao seguinte:

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

O esquema de exemplo será semelhante ao seguinte:

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

O exemplo seguinte mostra o esquema do **LiveEventConnectionRejected** eventos: 

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
| streamId | string | Identificador do stream ou da ligação. Codificador ou o cliente é responsável para adicionar este ID no URL de ingestão. |  
| ingestUrl | string | Ingestão de URL fornecido pelo evento em direto. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
| resultCode | string | O motivo pelo qual a ligação foi rejeitado. Os códigos de resultado estão listados na tabela seguinte. |

Os códigos de resultado são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão de incorreto |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Codificador de IP não está presente no IP configurada de lista de permissões |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Codificador não envia metadados sobre o fluxo. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Codec especificado não é suportada. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Foi recebido um fragmento antes que recebem e cabeçalho para esse fluxo. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Número de qualidades especificado excede o limite máximo permitido. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Velocidade de transmissão agregada excede o limite permitido máximo. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de hora para FLVTag de vídeo ou áudio é inválido de codificador RTMP. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

O exemplo seguinte mostra o esquema do **LiveEventEncoderConnected** eventos: 

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
| streamId | string | Identificador do stream ou da ligação. Codificador ou o cliente é responsável por fornecer este ID no URL de ingestão. |
| ingestUrl | string | Ingestão de URL fornecido pelo evento em direto. |
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

O exemplo seguinte mostra o esquema do **LiveEventEncoderDisconnected** eventos: 

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
| streamId | string | Identificador do stream ou da ligação. Codificador ou o cliente é responsável para adicionar este ID no URL de ingestão. |  
| ingestUrl | string | Ingestão de URL fornecido pelo evento em direto. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
| resultCode | string | O motivo para o codificador a desligar. É possível desligar anulações normais ou de um erro. Os códigos de resultado estão listados na tabela seguinte. |

Os códigos de resultado de erro são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | Sessão RTMP excedido o tempo limite após inatividade para o limite de tempo permitido. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de hora para FLVTag de vídeo ou áudio é inválido de codificador RTMP. |
| MPE_CAPACITY_LIMIT_REACHED | Codificador de envio de dados muito rápidos. |
| Códigos de erro desconhecido | Esses códigos de erro podem variar de erro de memória a duplicação de entradas no mapa de hash. |

Os códigos de resultado de anulações normais de desligamento são:

| Código de resultado | Descrição |
| ----------- | ----------- |
| S_OK | Codificador desligado com êxito. |
| MPE_CLIENT_TERMINATED_SESSION | Codificador desligado (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificador desligado (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Comando de reposição do canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | Foi recebido um comando de paragem de canal. |
| MPI_REST_API_CHANNEL_STOP | Canal em manutenção. |
| MPI_STREAM_HIT_EOF | Fluxo EOF é enviado pelo codificador. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

O exemplo seguinte mostra o esquema do **LiveEventIncomingDataChunkDropped** eventos: 

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
| trackType | string | Tipo da faixa (áudio / vídeo). |
| trackName | string | Nome da faixa. |
| Velocidade de transmissão | inteiro | Velocidade de transmissão da faixa. |
| timestamp | string | Timestamp do segmento de dados removida. |
| escala temporal | string | Escala temporal do período de tempo. |
| resultCode | string | Motivo da lista de segmentos de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo seguinte mostra o esquema do **LiveEventIncomingStreamReceived** eventos: 

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
| trackType | string | Tipo da faixa (áudio / vídeo). |
| trackName | string | Nome da faixa (qualquer um dos fornecidos pelo codificador ou, em caso de RTMP, servidor gera no *TrackType_Bitrate* formato). |
| Velocidade de transmissão | inteiro | Velocidade de transmissão da faixa. |
| ingestUrl | string | Ingestão de URL fornecido pelo evento em direto. |
| encoderIp | string  | IP do codificador. |
| encoderPort | string | Porta do codificador de onde vem esse fluxo. |
| timestamp | string | Primeira timestamp do segmento de dados recebido. |
| escala temporal | string | Escala temporal em que é representado timestamp. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo seguinte mostra o esquema do **LiveEventIncomingStreamsOutOfSync** eventos: 

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
| minLastTimestamp | string | Mínimo de carimbos de data / último entre todas as faixas (vídeos ou áudio). |
| typeOfTrackWithMinLastTimestamp | string | Tipo de controle (vídeo ou áudio) com o mínimo timestamp último. |
| maxLastTimestamp | string | Máximo de todos os carimbos entre todas as faixas (vídeos ou áudio). |
| typeOfTrackWithMaxLastTimestamp | string | Tipo de controlar (vídeo ou áudio) com o máximo timestamp último. |
| timescaleOfMinLastTimestamp| string | Obtém a escala temporal em que é representado "MinLastTimestamp".|
| timescaleOfMaxLastTimestamp| string | Obtém a escala temporal em que é representado "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo seguinte mostra o esquema do **LiveEventIncomingVideoStreamsOutOfSync** eventos: 

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
| firstTimestamp | string | Timestamp recebido para um dos níveis de qualidade/faixas de tipo de vídeo. |
| firstDuration | string | Duração do segmento de dados com timestamp primeiro. |
| secondTimestamp | string  | Timestamp recebido para algum outro nível de controle/qualidade do tipo vídeo. |
| SecondDuration | string | Duração do segmento de dados com o segundo timestamp. |
| escala temporal | string | Escala temporal de carimbos e duração.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo seguinte mostra o esquema do **LiveEventIngestHeartbeat** eventos: 

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
| trackType | string | Tipo da faixa (áudio / vídeo). |
| trackName | string | Nome da faixa (qualquer um dos fornecidos pelo codificador ou, em caso de RTMP, servidor gera no *TrackType_Bitrate* formato). |
| Velocidade de transmissão | inteiro | Velocidade de transmissão da faixa. |
| incomingBitrate | inteiro | Velocidade de transmissão calculada com base nos segmentos de dados provenientes do codificador. |
| lastTimestamp | string | Mais recente timestamp recebida um Roteiro na última 20 segundos. |
| escala temporal | string | Escala temporal em que são expressos carimbos. |
| overlapCount | inteiro | Número de segmentos de dados tinha overlapped carimbos de data / no último 20 segundos. |
| discontinuityCount | inteiro | Número de discontinuities observados nos últimos 20 segundos. |
| nonIncreasingCount | inteiro | Número de segmentos de dados carimbos de data no passado foram recebido nos últimos 20 segundos. |
| unexpectedBitrate | booleano | Se forem diferentes velocidades de transmissão de expected e actual ao limite permitido mais do que em última 20 segundos. É verdadeiro se e apenas se, incomingBitrate > = 2 * velocidade de transmissão ou incomingBitrate < = IncomingBitrate ou de velocidade de transmissão/2 = 0. |
| state | string | Estado do evento em direto. |
| bom estado de funcionamento | booleano | Indica se ingerir é bom estado de funcionamento com base nas contagens e sinalizadores. Bom estado de funcionamento é verdadeiro se overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo seguinte mostra o esquema do **LiveEventTrackDiscontinuityDetected** eventos: 

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
| trackType | string | Tipo da faixa (áudio / vídeo). |
| trackName | string | Nome da faixa (qualquer um dos fornecidos pelo codificador ou, em caso de RTMP, servidor gera no *TrackType_Bitrate* formato). |
| Velocidade de transmissão | inteiro | Velocidade de transmissão da faixa. |
| previousTimestamp | string | Timestamp do fragmento anterior. |
| newTimestamp | string | Timestamp do fragmento atual. |
| discontinuityGap | string | Lacuna entre acima dois carimbos. |
| escala temporal | string | Escala temporal na qual lacuna timestamp e descontinuidade são representados. |

### <a name="common-event-properties"></a>Propriedades de evento comum

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | O tópico de EventGrid. Esta propriedade tem o ID de recurso para a conta de Media Services. |
| assunto | string | O caminho de recurso para o canal de serviços de multimédia sob a conta de Media Services. Concatenar dão o tópico e o assunto é o recurso de ID da tarefa. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| ID | string | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos de serviços de multimédia. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

## <a name="next-steps"></a>Passos Seguintes

[Se registrar para eventos de alteração de estado de tarefa](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Consulte também

- [SDK de .NET de EventGrid que inclui eventos de serviço de multimédia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de serviços de multimédia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
