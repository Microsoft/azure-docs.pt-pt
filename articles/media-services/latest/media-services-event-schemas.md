---
title: Rede de Eventos Azure schemas para eventos de Serviços de Media
description: Descreve as propriedades que estão fornecidas para eventos de Media Services com a Rede de Eventos Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: d4a206bbddedfe9f23a943df27c6ac4b5fe17e8a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251352"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Rede de Eventos Azure schemas para eventos de Serviços de Media

Este artigo fornece os esquemas e propriedades para eventos de Serviços de Media.

Para obter uma lista de scripts e tutoriais de amostras, consulte [a fonte do evento Media Services.](../../event-grid/event-sources.md#azure-subscriptions)

## <a name="job-related-event-types"></a>Tipos de eventos relacionados com o trabalho

A Media Services emite os tipos de eventos relacionados com o **Trabalho** descritos abaixo. Existem duas categorias para os eventos relacionados com o **Trabalho:** "Monitorizar as alterações do Estado de Trabalho" e "Monitorizar as Alterações do Estado da Saída de Emprego". 

Pode inscrever-se em todos os eventos subscrevendo o evento JobStateChange. Ou, pode subscrever apenas eventos específicos (por exemplo, estados finais como JobErrored, JobFinished e JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Monitorização das alterações do Estado de trabalho

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Obtenha um evento para todas as mudanças no Estado de Trabalho. |
| Microsoft.Media.JobScheduled| Obtenha um evento quando Job transitar para o estado programado. |
| Microsoft.Media.JobProcessing| Obtenha um evento quando Job transitar para o estado de processamento. |
| Microsoft.Media.JobCanceling| Obtenha um evento quando Job transitar para o estado de cancelamento. |
| Microsoft.Media.JobFinished| Obtenha um evento quando Job transitar para o estado final. Este é um estado final que inclui saídas de emprego.|
| Microsoft.Media.JobCanceled| Obtenha um evento quando Job transitar para o estado cancelado. Este é um estado final que inclui saídas de emprego.|
| Microsoft.Media.JobErrored| Obtenha um evento quando Job transitar para o estado de erro. Este é um estado final que inclui saídas de emprego.|

Veja os exemplos de [Schema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-state-changes"></a>Monitorização das alterações do estado de produção de emprego

Um trabalho pode conter várias saídas de trabalho (se configurar a transformação para ter várias saídas de emprego.) Se quiser acompanhar os detalhes da saída de trabalho individual, ouça um evento de mudança de saída de emprego.

Cada **Job** vai estar a um nível mais alto do que o **JobOutput,** assim os eventos de saída de emprego são despedidos dentro de um trabalho correspondente. 

As mensagens de erro em `JobFinished`, `JobCanceled`, `JobError` os resultados agregados para cada saída de trabalho – quando todas estiverem concluídas. Enquanto que, a saída de trabalho dispara à medida que cada tarefa termina. Por exemplo, se tiver uma saída de codificação, seguida de uma saída de Video Analytics, obteria dois eventos disparados como eventos de saída de emprego antes do evento final jobFinished disparar com os dados agregados.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Obtenha um evento para todas as mudanças de Estado de saída de emprego. |
| Microsoft.Media.JobOutputScheduled| Obtenha um evento quando a saída de Trabalho transite para o estado programado. |
| Microsoft.Media.JobOutputProcessing| Obtenha um evento quando a saída de Emprego transite para o estado de processamento. |
| Microsoft.Media.JobOutputCanceling| Obtenha um evento quando a saída de Trabalho transite para o estado de cancelamento.|
| Microsoft.Media.JobOutputFinished| Obtenha um evento quando a saída de Job transite para o estado final.|
| Microsoft.Media.JobOutputCanceled| Obtenha um evento quando a saída de Trabalho transite para o estado cancelado.|
| Microsoft.Media.JobOutputErrored| Obtenha um evento quando a saída de Trabalho transite para o estado de erro.|

Veja os exemplos de [Schema](#event-schema-examples) que se seguem.

### <a name="monitoring-job-output-progress"></a>Monitorização do progresso da produção de emprego

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Este evento reflete o progresso do processamento de emprego, de 0% a 100%. O serviço tenta enviar um evento se houve um aumento de 5% ou mais no valor de progresso ou se foi mais de 30 segundos desde o último evento (batimento cardíaco). O valor do progresso não está garantido para começar em 0%, nem chegar aos 100%, nem é garantido que aumente a um ritmo constante ao longo do tempo. Este evento não deve ser utilizado para determinar se o processamento foi concluído – em vez disso, deve utilizar os eventos de mudança de Estado.|

Veja os exemplos de [Schema](#event-schema-examples) que se seguem.

## <a name="live-event-types"></a>Tipos de eventos ao vivo

A Media Services também emite os tipos de eventos **ao vivo** descritos abaixo. Existem duas categorias para os eventos **ao vivo:** eventos de nível de streaming e eventos de nível de pista. 

### <a name="stream-level-events"></a>Eventos de nível de fluxo

Os eventos de nível de fluxo são elevados por fluxo ou conexão. Cada evento tem um parâmetro `StreamId` que identifica a ligação ou o fluxo. Cada fluxo ou ligação tem uma ou mais faixas de diferentes tipos. Por exemplo, uma ligação de um codificador pode ter uma faixa de áudio e quatro faixas de vídeo. Os tipos de eventos de fluxo são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de ligação do codificador é rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | O Codificador estabelece a ligação com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDisconnected | O codificador desliga-se. |

Veja os exemplos de [Schema](#event-schema-examples) que se seguem.

### <a name="track-level-events"></a>Eventos ao nível da pista

Os eventos ao nível das pistas são elevados por pista. 

> [!NOTE]
> Todos os eventos ao nível da pista são levantados após a ligação de um codificador ao vivo.

Os tipos de eventos ao nível da pista são:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | O servidor de mídia deixa cair a parte dos dados porque é tarde demais ou tem um carimbo de tempo sobreposto (o carimbo temporal de novos dados é inferior ao tempo final do pedaço de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe o primeiro pedaço de dados para cada faixa no fluxo ou ligação. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia deteta que os fluxos de áudio e vídeo estão dessincronizados. Utilize como aviso porque a experiência do utilizador pode não ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia deteta que qualquer um dos dois fluxos de vídeo provenientes de codificador externo está dessincronizado. Utilize como aviso porque a experiência do utilizador pode não ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada pista quando o evento ao vivo está em execução. Fornece o resumo da saúde.<br/><br/>Após a ligação inicial do codificador, o evento do batimento cardíaco continua a emitir a cada 20 segundos se o codificador ainda está ligado ou não. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia deteta descontinuidade na faixa de entrada. |

Veja os exemplos de [Schema](#event-schema-examples) que se seguem.

## <a name="event-schema-examples"></a>Exemplos de esquemas de eventos

### <a name="jobstatechange"></a>JobStateChange

O exemplo que se segue mostra o esquema do evento **JobStateChange:** 

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
| anteriorEstado | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho a ser notificado neste evento. Por exemplo, "Agendado: O trabalho está pronto para começar" ou "Terminado: O trabalho está terminado".|

Onde o estado de trabalho pode ser um dos valores: *Fila,* *Agendado,* *Processamento,* *Terminado,* *Erro,* *Cancelado,* *Cancelamento*

> [!NOTE]
> *A fila* só vai estar presente na propriedade **anterior do Estado,** mas não na propriedade do **Estado.**

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Para cada mudança não definitiva de estado de trabalho (como JobScheduled, JobProcessing, JobCanceling), o esquema de exemplo é semelhante ao seguinte:

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

Para cada mudança final de estado de trabalho (como JobFinished, JobCanceled, JobErrored), o esquema de exemplo parece semelhante ao seguinte:

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
| outputs | Array | Obtém as saídas de trabalho.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

O exemplo que se segue mostra o esquema do evento **JobOutputStateChange:**

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

Para cada mudança de estado JobOutput, o esquema de exemplo parece semelhante ao seguinte:

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

O exemplo que se segue mostra o esquema do evento **LiveEventConnectionRejected:** 

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
| streamId | string | Identificador do fluxo ou ligação. O codificador ou cliente é responsável por adicionar este ID no URL ingerir. |  
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porto do codificador de onde este riacho está vindo. |
| resultCode | string | A razão pela qual a ligação foi rejeitada. Os códigos de resultados estão listados na tabela seguinte. |

Pode encontrar os códigos de erro nos [códigos](live-event-error-codes.md)de erro do evento ao vivo .

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

O exemplo que se segue mostra o esquema do evento **LiveEventEncoderConnected:** 

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
| streamId | string | Identificador do fluxo ou ligação. O codificador ou cliente é responsável por fornecer este ID no URL ingerir. |
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porto do codificador de onde este riacho está vindo. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

O exemplo que se segue mostra o esquema do evento **LiveEventEncoderDisconnected:** 

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
| streamId | string | Identificador do fluxo ou ligação. O codificador ou cliente é responsável por adicionar este ID no URL ingerir. |  
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |  
| encoderIp | string | IP do codificador. |
| encoderPort | string | Porto do codificador de onde este riacho está vindo. |
| resultCode | string | A razão para a desconexão do codificador. Pode ser uma desconexão graciosa ou de um erro. Os códigos de resultados estão listados na tabela seguinte. |

Pode encontrar os códigos de erro nos [códigos](live-event-error-codes.md)de erro do evento ao vivo .

Os graciosos códigos de resultados de desconexão são:

| Código de resultados | Descrição |
| ----------- | ----------- |
| S_OK | O codificador desligou-se com sucesso. |
| MPE_CLIENT_TERMINATED_SESSION | Codificador desligado (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificador desligado (FMP4). |
| MPI_REST_API_CHANNEL_RESET | O comando de reset do canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | Comando de paragem do canal recebido. |
| MPI_REST_API_CHANNEL_STOP | Canal em manutenção. |
| MPI_STREAM_HIT_EOF | O fluxo EOF é enviado pelo codificador. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

O exemplo que se segue mostra o esquema do evento **LiveEventIncomingDataChunkDrop:** 

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
| trackType | string | Tipo da faixa (Áudio / Vídeo). |
| trackName | string | Nome da pista. |
| bitrate | número inteiro | Bitrate da pista. |
| carimbo de data/hora | string | O carimbo temporal do pedaço de dados caiu. |
| timescale | string | Escala temporal do carimbo temporal. |
| resultCode | string | A razão da queda do pedaço de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp.** |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo que se segue mostra o esquema do evento **LiveEventIncomingStreamReceived:** 

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
| trackType | string | Tipo da faixa (Áudio / Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| ingestUrl | string | Ingest URL fornecido pelo evento ao vivo. |
| encoderIp | string  | IP do codificador. |
| encoderPort | string | Porto do codificador de onde este riacho está vindo. |
| carimbo de data/hora | string | Primeira vez que o pedaço de dados recebido. |
| timescale | string | Escala de tempo em que o carimbo de tempo está representado. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo que se segue mostra o esquema do evento **LiveEventIncomingStreamsOutOfSync:** 

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
| minLastTimestamp | string | Mínimo de selos de última vez entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMinLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com selos mínimos da última vez. |
| maxLastTimestamp | string | Máximo de todos os carimbos de tempo entre todas as faixas (áudio ou vídeo). |
| typeOfTrackWithMaxLastTimestamp | string | Tipo da faixa (áudio ou vídeo) com carimbo máximo da última vez. |
| timescaleOfMinLastTimestamp| string | Obtém o calendário em que "MinLastTimestamp" está representado.|
| timescaleOfMaxLastTimestamp| string | Obtém o calendário em que "MaxLastTimestamp" está representado.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo que se segue mostra o esquema do evento **LiveEventIncomingVideoStreamsOutOfSync:** 

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
| primeiroCarimbo time | string | Carimbo de tempo recebido para uma das faixas/níveis de qualidade do tipo de vídeo. |
| firstDuration | string | Duração do pedaço de dados com carimbo de primeira hora. |
| segunda Marca tempora | string  | Carimbo de tempo recebido para outro nível de pista/qualidade do tipo de vídeo. |
| secondDuration | string | Duração do pedaço de dados com segunda marca de tempo. |
| timescale | string | Escala temporal de selos e duração.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo que se segue mostra o esquema do evento **LiveEventIngestHeartbeat:** 

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
| trackType | string | Tipo da faixa (Áudio / Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| incomingBitrate | número inteiro | Bitrate calculado com base em pedaços de dados provenientes de codificador. |
| últimoTimestamp | string | O último carimbo de tempo recebido para uma pista nos últimos 20 segundos. |
| timescale | string | Calendário em que os selos temporais são expressos. |
| sobreposiçãoCount | número inteiro | O número de pedaços de dados tinha sobreposto os selos temporais nos últimos 20 segundos. |
| discontinuityCount | número inteiro | Número de descontinuidades observadas nos últimos 20 segundos. |
| nonIncreasingCount | número inteiro | O número de pedaços de dados com carimbos temporais no passado foi recebido nos últimos 20 segundos. |
| unexpectedBitrate | bool | Se esperado e os bitrates reais diferem mais do que o limite permitido nos últimos 20 segundos. É verdade se e só se, incomingBitrate >= 2* bitrate OR incomingBitrate <= bitrate/2 OR IncomingBitrate = 0. |
| state | string | Estado do evento ao vivo. |
| healthy | bool | Indica se a ingestão é saudável com base nas contagens e bandeiras. Saudável é verdade se sobreposiçãoCount = 0 &amp; descontinuidadeCount = 0&& nonIncreasingCount = 0 && inesperadoBitrate = falso. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo seguinte mostra o esquema do evento **LiveEventTrackDiscontinuidadeDetected:** 

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
| trackType | string | Tipo da faixa (Áudio / Vídeo). |
| trackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera em *formato TrackType_Bitrate).* |
| bitrate | número inteiro | Bitrate da pista. |
| anteriorTimestamp | string | Carimbo de tempo do fragmento anterior. |
| newTimestamp | string | Carimbo de tempo do fragmento atual. |
| discontinuityGap | string | Distância entre dois selos temporais acima. |
| timescale | string | O calendário em que se representa o intervalo de tempo e a descontinuidade. |

### <a name="common-event-properties"></a>Propriedades comuns do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | O tema EventGrid. Esta propriedade tem o ID de recursos para a conta de Serviços de Media. |
| subject | string | A rota de recursos para o canal media services no âmbito da conta Media Services. Concatenar o tópico e o sujeito dão-lhe a identificação do recurso para o trabalho. |
| eventType | string | Um dos tipos de eventos registados para esta fonte do evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventoTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento dos Media Services. |
| dataVersion | string | A versão esquema do objeto de dados. A editora define a versão do esquema. |
| metadataVersion | string | A versão de esquema dos metadados do evento. A Grelha de Eventos define o esquema das propriedades de alto nível. A Grelha de Eventos fornece este valor. |

## <a name="next-steps"></a>Passos seguintes

[Registe-se para eventos de mudança de estado de emprego](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Consulte também

- [EventGrid .NET SDK que inclui eventos do Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Media](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Códigos de erro do Evento Ao Vivo](live-event-error-codes.md)
