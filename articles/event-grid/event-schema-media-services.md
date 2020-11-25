---
title: Azure Media Services como fonte de Rede de Eventos
description: Descreve as propriedades que são fornecidas para eventos de Serviços de Mídia com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c5953cae7364131eefcec97d3375404c85e963
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015218"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Azure Media Services como fonte de rede de eventos

Este artigo fornece os esquemas e propriedades para eventos de Media Services.

## <a name="job-related-event-types"></a>Tipos de eventos relacionados com o trabalho

Os Serviços de Comunicação Social emitem os tipos de eventos relacionados com **o Job** descritos abaixo. Existem duas categorias para os eventos relacionados com o **Trabalho:** "Monitorização das Alterações do Estado de Trabalho" e "Monitorização das Alterações do Estado de Saída de Emprego". 

Pode inscrever-se em todos os eventos subscrevendo o evento JobStateChange. Ou, só pode subscrever eventos específicos (por exemplo, estados finais como JobErrored, JobFinished e JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorização das alterações do Estado de Trabalho

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Obtenha um evento para todas as mudanças no Estado de Trabalho. |
| Microsoft.Media.JobScheduled| Obtenha um evento quando Job transitar para o estado agendado. |
| Microsoft.Media.JobProcessing| Obtenha um evento quando Job transita para o estado de processamento. |
| Microsoft.Media.JobCanceling| Obtenha um evento quando Job transitar para o estado de cancelamento. |
| Microsoft.Media.JobFinished| Obtenha um evento quando Job transita para estado final. Este é um estado final que inclui saídas de Emprego.|
| Microsoft.Media.JobCanceled| Obtenha um evento quando Job transitar para estado cancelado. Este é um estado final que inclui saídas de Emprego.|
| Microsoft.Media.JobErrored| Obtenha um evento quando Job transita para estado de erro. Este é um estado final que inclui saídas de Emprego.|

Veja [os exemplos de Schema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-state-changes"></a>Monitorização das alterações do estado de saída de emprego

Um trabalho pode conter várias saídas de trabalho (se configurar a transformação para ter várias saídas de trabalho.) Se quiser acompanhar os detalhes da saída de cada trabalho, ouça um evento de mudança de saída de emprego.

Cada **Job** vai estar a um nível mais alto do que **o JobOutput,** assim os eventos de saída de emprego são despedidos dentro de um emprego correspondente. 

As mensagens de erro em `JobFinished` , `JobCanceled` , `JobError` produção os resultados agregados para cada saída de trabalho - quando todas elas estão terminadas. Enquanto que, os eventos de saída de trabalho disparam à medida que cada tarefa termina. Por exemplo, se tiver uma saída codificadora, seguida de uma saída de Video Analytics, obteria dois eventos disparando como eventos de saída de emprego antes do evento jobfinished final disparar com os dados agregados.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Obtenha um evento para todas as mudanças de Estado de saída de emprego. |
| Microsoft.Media.JobOutputScheded| Obtenha um evento quando a saída de Job transitar para o estado programado. |
| Microsoft.Media.JobOutputProcessamento| Obtenha um evento quando a saída de Job transitar para o estado de processamento. |
| Microsoft.Media.JobOutputCanceling| Obtenha um evento quando a saída de Job transitar para o estado de cancelamento.|
| Microsoft.Media.JobOutputFinished| Obtenha um evento quando a saída de Job transitar para estado final.|
| Microsoft.Media.JobOutputCanceled| Obtenha um evento quando a saída de Job transitar para estado cancelado.|
| Microsoft.Media.JobOutputErrored| Obtenha um evento quando a saída de Job transite para o estado de erro.|

Veja [os exemplos de Schema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-progress"></a>Monitorização do progresso da produção de emprego

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Este evento reflete o progresso no processamento de emprego, de 0% para 100%. O serviço tenta enviar um evento se tiver havido um aumento de 5% ou maior no valor de progresso ou se já se passou mais de 30 segundos desde o último evento (batimento cardíaco). O valor de progressão não está garantido para começar em 0%, ou para chegar a 100%, nem é garantido que aumente a uma taxa constante ao longo do tempo. Este evento não deve ser utilizado para determinar que o processamento foi concluído – em vez disso, deve utilizar os eventos de alteração do Estado.|

Veja [os exemplos de Schema](#event-schema-examples) que se seguem.

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Os Serviços de Comunicação Social também emitem os tipos de eventos **Live** descritos abaixo. Existem duas categorias para os eventos **ao vivo:** eventos ao nível do streaming e eventos ao nível da pista. 

### <a name="stream-level-events"></a>Eventos ao nível do fluxo

Os eventos de nível de fluxo são levantados por fluxo ou ligação. Cada evento tem um `StreamId` parâmetro que identifica a ligação ou fluxo. Cada fluxo ou ligação tem uma ou mais faixas de diferentes tipos. Por exemplo, uma ligação de um codificar pode ter uma faixa de áudio e quatro faixas de vídeo. Os tipos de eventos de streaming são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de ligação do Codificação é rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | A Encoder estabelece a ligação com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDis ligados | O codificadores desliga. |

Veja [os exemplos de Schema](#event-schema-examples) que se seguem.

### <a name="track-level-events"></a>Eventos ao nível da pista

Os eventos ao nível da pista são aumentados por pista. 

> [!NOTE]
> Todos os eventos ao nível da pista são levantados depois de um codificadora ao vivo estar ligado.

Os tipos de eventos ao nível da pista são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDroped | O servidor de mídia deixa cair o pedaço de dados porque é demasiado tarde ou tem um relógio sobreposto (o tempotabo de novo pedaço de dados é inferior ao tempo final do pedaço de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe o primeiro pedaço de dados para cada faixa no fluxo ou ligação. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia deteta streams de áudio e vídeo fora de sincronização. Use como aviso porque a experiência do utilizador não pode ser impactada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia deteta que qualquer um dos dois streams de vídeo provenientes de codificadores externos estão dessincronizados. Use como aviso porque a experiência do utilizador não pode ser impactada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando o evento está em execução. Fornece um resumo de saúde ingerido.<br/><br/>Após o codificador ter sido inicialmente ligado, o evento de batimentos cardíacos continua a emitir a cada 20 segundos se o codificador ainda está ligado ou não. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia deteta a descontinuidade na faixa de entrada. |

Veja [os exemplos de Schema](#event-schema-examples) que se seguem.

## <a name="event-schema-examples"></a>Exemplos de esquemas de eventos

### <a name="jobstatechange"></a>JobStateChange

O exemplo a seguir mostra o esquema do evento **JobStateChange:** 

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
| Estado anterior | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho a ser notificado neste evento. Por exemplo, "Agendado: O trabalho está pronto para começar" ou "Terminado: O trabalho está terminado".|

Onde o estado de Trabalho pode ser um dos valores: *Fila,* *Programado,* *Processamento,* *Concluído,* *Erro,* *Cancelado,* *Cancelamento*

> [!NOTE]
> *A fila* só vai estar presente na propriedade **anterior do Estado,** mas não na propriedade do **Estado.**

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheded, JobProcessing, JobCanceling

Para cada mudança de estado de trabalho não final (como JobScheduled, JobProcessing, JobCanceling), o esquema de exemplo é semelhante ao seguinte:

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

Para cada mudança final do estado de Emprego (como JobFinished, JobCanceled, JobErrored), o esquema de exemplo é semelhante ao seguinte:

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
| saídas | Matriz | Obtém as saídas do Trabalho.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

O exemplo a seguir mostra o esquema do evento **JobOutputStateChange:**

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

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheded, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Para cada mudança de estado jobOutput, o esquema de exemplo é semelhante ao seguinte:

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

O exemplo a seguir mostra o esquema do **evento LiveEventConnectionRejected:** 

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
| streamId | string | Identificador do fluxo ou ligação. A Codificação ou o cliente é responsável por adicionar este ID no URL ingerido. |  
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |  
| codificarIp | string | IP do codificar. |
| encoderPort | string | Porto do codificar de onde vem este riacho. |
| resultadosDesco | string | A razão pela qual a ligação foi rejeitada. Os códigos de resultados estão listados na tabela seguinte. |

Pode encontrar os códigos de resultado de erro nos [códigos de erro do Evento ao vivo](../media-services/latest/live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoder Ligado

O exemplo a seguir mostra o esquema do **evento LiveEventEncoderConnected:** 

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
| streamId | string | Identificador do fluxo ou ligação. A Encoder ou o cliente é responsável por fornecer este ID no URL ingerido. |
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |
| codificarIp | string | IP do codificar. |
| encoderPort | string | Porto do codificar de onde vem este riacho. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDis ligados

O exemplo a seguir mostra o esquema do **evento LiveEventEncoderDis:** 

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
| streamId | string | Identificador do fluxo ou ligação. A Codificação ou o cliente é responsável por adicionar este ID no URL ingerido. |  
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |  
| codificarIp | string | IP do codificar. |
| encoderPort | string | Porto do codificar de onde vem este riacho. |
| resultadosDesco | string | A razão para a desconexão do codificante. Pode ser uma desconexão graciosa ou de um erro. Os códigos de resultados estão listados na tabela seguinte. |

Pode encontrar os códigos de resultado de erro nos [códigos de erro do Evento ao vivo](../media-services/latest/live-event-error-codes.md).

Os graciosos códigos de resultados de desconexão são:

| Código do resultado | Descrição |
| ----------- | ----------- |
| S_OK | Codificação desligada com sucesso. |
| MPE_CLIENT_TERMINATED_SESSION | Codificação desligada (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificação desligada (FMP4). |
| MPI_REST_API_CHANNEL_RESET | O comando de reset do canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | O comando de paragem do canal foi recebido. |
| MPI_REST_API_CHANNEL_STOP | Canal em manutenção. |
| MPI_STREAM_HIT_EOF | O fluxo EOF é enviado pelo codificar. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDroped

O exemplo a seguir mostra o esquema do **evento LiveEventIncomingDataChunkDropped:** 

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
| trackType | string | Tipo da faixa (Áudio/ Vídeo). |
| trackName | string | O nome da pista. |
| bitrate | número inteiro | Bitrate da pista. |
| carimbo de data/hora | string | O tempotamped do pedaço de dados caiu. |
| escala de tempo | string | Calendário da estada. |
| resultadosDesco | string | Razão da queda do pedaço de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp.** |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo a seguir mostra o esquema do **evento LiveEventIncomingStreamReceived:** 

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
| trackType | string | Tipo da faixa (Áudio/ Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificader ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |
| codificarIp | string  | IP do codificar. |
| encoderPort | string | Porto do codificar de onde vem este riacho. |
| carimbo de data/hora | string | Primeiro tempo de data do pedaço de dados recebido. |
| escala de tempo | string | Calendário em que a estada de tempo é representada. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamsOutOfSync:** 

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
| minLastTimestamp | string | Mínimo de últimos tempos de tempos entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMinLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com o mínimo de última hora. |
| maxLastTimestamp | string | Máximo de todos os tempos de todos os tempos entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMaxLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com o máximo de última hora. |
| timescaleOfMinLastTimestamp| string | Obtém o calendário em que está representado o "MinLastTimestamp".|
| timescaleOfMaxLastTimestamp| string | Obtém o calendário em que está representado o "MaxLastTimestamp".|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingVideoStreamsOutOfSync:** 

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
| primeira hora de temperatura | string | Relógio de tempo recebido para uma das faixas/níveis de qualidade do tipo de vídeo. |
| firstDuration | string | Duração do pedaço de dados com primeira estamp de tempo. |
| segunda hora de temperatura | string  | Relógio recebido para outro nível de faixa/qualidade do tipo de vídeo. |
| segundaDuração | string | Duração do pedaço de dados com segunda placa de tempo. |
| escala de tempo | string | Escala de tempo de tempos de tempo e duração.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo a seguir mostra o esquema do evento **LiveEventIngestHeartbeat:** 

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
| trackType | string | Tipo da faixa (Áudio/ Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificader ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| incomingBitrate | número inteiro | Bitrate calculado com base em pedaços de dados provenientes do codificadores. |
| última hora de temperatura | string | Última data de tempo recebida para uma pista nos últimos 20 segundos. |
| escala de tempo | string | Calendário em que os prazos são expressos. |
| sobreposiçãoCount | número inteiro | O número de pedaços de dados tinha sobrepostos os tempos nos últimos 20 segundos. |
| descontinuidadeCount | número inteiro | Número de descontinuidades observadas nos últimos 20 segundos. |
| nonIncreasingCount | número inteiro | O número de pedaços de dados com cartões temporais no passado foram recebidos nos últimos 20 segundos. |
| inesperadoBitrate | bool | Se esperado e os bitrates reais diferem mais do que o limite permitido nos últimos 20 segundos. É verdade se e somente se, incomingBitrate >= 2* bitrate ou incomingBitrate <= bitrate/2 OU IncomingBitrate = 0. |
| state | string | Estado do evento ao vivo. |
| saudável | bool | Indica se a ingestão é saudável com base nas contagens e bandeiras. Saudável é verdade se se sobrepor oCount = 0 && descontinuidadeCount = 0 && nonIncreasingCount = 0 && inesperadoBitrate = falso. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo a seguir mostra o esquema do **evento LiveEventTrackDiscontinuityDetected:** 

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
| trackType | string | Tipo da faixa (Áudio/ Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificader ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| anteriorestampia de tempo | string | Tempotam do fragmento anterior. |
| nova hora | string | Tempotam do fragmento atual. |
| descontínuaGap | string | Intervalo entre dois picos de tempo. |
| escala de tempo | string | Calendário em que estão representados tanto o intervalo de tempo como o fosso de descontinuidade. |

### <a name="common-event-properties"></a>Propriedades comuns do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | O tema EventGrid. Esta propriedade tem o ID de recursos para a conta dos Serviços de Comunicação. |
| Assunto | string | A trajetória de recursos para o canal De Serviços de Mídia sob a conta dos Serviços de Comunicação. Concatenando o tópico e o assunto dão-lhe o iD de recursos para o trabalho. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados do evento dos Serviços de Comunicação Social. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

## <a name="next-steps"></a>Passos seguintes

[Registe-se para eventos de mudança de estado de trabalho](../media-services/latest/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Ver também

- [EventGrid .NET SDK que inclui eventos de Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Códigos de erro do Evento Ao Vivo](../media-services/latest/live-event-error-codes.md)
