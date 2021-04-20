---
title: Visão geral da chamada de serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral da funcionalidade de Gravação de Chamadas e APIs.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730608"
---
# <a name="calling-recording-overview"></a>Chamando a visão geral da gravação

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Muitos países e estados têm leis e regulamentos que se aplicam à gravação de chamadas PSTN, voz e vídeo, que muitas vezes exigem que os utilizadores consintam na gravação das suas comunicações. É da sua responsabilidade utilizar as capacidades de gravação de chamadas em conformidade com a lei. Deve obter o consentimento das partes das comunicações gravadas de forma a cumprir com as leis aplicáveis a cada participante.

> [!NOTE]
> Os regulamentos em torno da manutenção de dados pessoais requerem a capacidade de exportar dados dos utilizadores. Para suportar estes requisitos, os ficheiros de registo de metadados incluem o participanteId para cada participante de chamada na `participants` matriz. Pode cruzar as ressonâncias magnéticas na matriz com as identidades internas do `participants` utilizador para identificar os participantes numa chamada. Um exemplo de um ficheiro de metadados de gravação é fornecido abaixo para referência.

Call Recording fornece um conjunto de APIs para iniciar, parar, parar e retomar a gravação. Estas APIs podem ser acedidas a partir da lógica de negócio do lado do servidor ou através de eventos desencadeados pelas ações do utilizador. A produção de mídia gravada está em `MP4 Audio+Video` formato, que é o mesmo formato que as Equipas usam para gravar meios de comunicação. As notificações relacionadas com os meios de comunicação e os metadados são emitidas através da Grelha de Eventos. As gravações são armazenadas durante 48 horas em armazenamento temporário incorporado para recuperação e movimento para uma solução de armazenamento de longo prazo de escolha. 

## <a name="run-time-control-apis"></a>APIs de controlo de tempo de execução
As APIs de controlo de tempo de execução podem ser usadas para gerir a gravação através de detonadores de lógica de negócio interno, como uma aplicação que cria uma chamada de grupo e grava a conversa, ou a partir de uma ação desencadeada pelo utilizador que diz à aplicação do servidor para começar a gravar. Em qualquer dos `<conversation-id>` cenários, é necessário registar uma reunião ou chamada específica. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Obter ID de conversação a partir de uma chamada iniciada pelo servidor

A `ConversationId` é devolvido através do `Microsoft.Communication.CallLegStateChanged` evento. Esta notificação do evento é emitida após a criação de uma chamada. Pode ser encontrado no `data.ConversationId` campo. Este valor pode ser usado diretamente como `{conversationId}` parâmetro em APIs de controlo de tempo de execução:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Obter a iD de conversação de um utilizador desencadeou evento no cliente

A partir da `@azure/communication-calling` biblioteca JavaScript, depois de estabelecer uma chamada invocada para obter o `let result = call.info.getConversationUrl()` , em `conversationUrl` seguida, **Base64Url codificar o `conversationUrl` para obter o para ser usado nas `{conversationId}` APIs de controlo de tempo de execução**. A codificação pode ser feita quer no cliente, quer no envio do evento para o servidor, quer para o lado do servidor.

Note que o `conversationUrl` *deve* ser codificado Base64Url, não deve ser confundido apenas com codificação Base64 (isto é, btoa).                                                            

### <a name="start-recording"></a>Iniciar gravação

#### <a name="request"></a>Pedir

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**SDK C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Resposta

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Receba o estado de gravação de chamadas

#### <a name="request"></a>Pedir

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**SDK C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Resposta

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Parar gravação
#### <a name="request"></a>Pedir
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Resposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Gravação de pausa
A pausa e o reinício da gravação de chamadas permitem-lhe saltar a gravação de uma parte de uma chamada ou reunião e retomar a gravação num único ficheiro. 
#### <a name="request"></a>Pedir
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Resposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Retomar a gravação
#### <a name="request"></a>Pedir
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**SDK C#**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Resposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Tipos de saída de mídia
A gravação de chamadas suporta atualmente o formato de saída de MP4 de áudio+vídeo misto. A comunicação social de saída corresponde a gravações de reuniões produzidas através da gravação da Microsoft Teams.

| Tipo de Canal | Formato de Conteúdo | Vídeo | Áudio |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 1920x1080 8 Vídeo FPS de todos os participantes no arranjo de azulejos predefinidos | 16kHz mp4a áudio misto de todos os participantes |

## <a name="event-grid-notifications"></a>Notificações da Grelha de Eventos
Uma notificação de Grade de `Microsoft.Communication.RecordingFileStatusUpdated` Eventos é publicada quando uma gravação está pronta para ser recuperada, normalmente 1-2 minutos após o processo de gravação ter terminado (por exemplo, a reunião terminou, a gravação parou). As notificações de eventos de gravação incluem um documento ID, que pode ser usado para recuperar tanto os meios de comunicação gravados como um ficheiro de metadados de gravação:

- <Azure_Communication_Service_Endpoint>/gravação/download/{documentId}
- <Azure_Communication_Service_Endpoint>/gravação/download/{documentId}/metadados

O código de amostra para lidar com notificações da grelha de eventos e descarregar ficheiros de gravação e metadados pode ser encontrado [aqui](../../quickstarts/voice-video-calling/download-recording-file-sample.md). 

### <a name="notification-schema"></a>Esquema de Notificação
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Download de Ficheiros

> A Azure Communication Services fornece armazenamento de mídia de curto prazo para gravações. **Exporte qualquer conteúdo registado que deseje preservar dentro de 48 horas.** Após 48 horas, as gravações deixarão de estar disponíveis.

### <a name="download-recording"></a>Transferência da gravação
#### <a name="request"></a>Pedir
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Resposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Baixar metadados de gravação
#### <a name="request"></a>Pedir
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Resposta
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
