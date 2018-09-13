---
title: Receber eventos do Azure Event Grid para um ponto final HTTP
description: Descreve como validar um ponto final HTTP, em seguida, receber e anular a serialização de eventos do Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: babanisa
ms.openlocfilehash: 1ff0cf9d2733d7ebb6e739ce44da6442ffc26c1c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649858"
---
# <a name="receive-events-to-an-http-endpoint"></a>Receber eventos para um ponto final HTTP

Este artigo descreve como [validar um ponto final HTTP](security-authentication.md#webhook-event-delivery) para receber eventos a partir de uma subscrição de evento e, em seguida, receber e anular a serialização de eventos. Este artigo utiliza uma função do Azure para fins de demonstração, no entanto, os mesmos conceitos aplicam-se independentemente de onde o aplicativo é hospedado.

> [!NOTE]
> É **vivamente** recomendado que utilize um [acionador do Event Grid](../azure-functions/functions-bindings-event-grid.md) quando acionar uma função do Azure com o Event Grid. A utilização de aqui um acionador de WebHook genérico é demonstrativa.

## <a name="prerequisites"></a>Pré-requisitos

* Precisará de uma aplicação de funções com um [função acionada por HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Adicionar dependências

Se estiver a desenvolver no .NET, [adicionar uma dependência](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) à sua função para o `Microsoft.Azure.EventGrid` [pacote Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). SDKs para outros idiomas estão disponíveis através da [SDKs publicar](./sdk-overview.md#data-plane-sdks) referência. Estes pacotes contêm os modelos para tipos de eventos nativos, como `EventGridEvent`, `StorageBlobCreatedEventData`, e `EventHubCaptureFileCreatedEventData`.

Clique no link "Ver ficheiros" na sua função do Azure (a maioria dos painel da direita do portal de funções do Azure) e crie um ficheiro chamado Project. Adicione o seguinte conteúdo para o `project.json` de ficheiro e guarde-o:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.3.0"
      }
    }
   }
}
```

![Pacote de NuGet foi adicionado](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validação do ponto final

A primeira coisa que deseja fazer é lidar com `Microsoft.EventGrid.SubscriptionValidationEvent` eventos. Sempre que alguém assina um evento, o Event Grid envia um evento de validação para o ponto final com um `validationCode` no payload de dados. O ponto final é necessário para o eco isso novamente no corpo da resposta à [provar o ponto final é válido e pertencentes à empresa por si](security-authentication.md#webhook-event-delivery). Se estiver a utilizar um [acionador do Event Grid](../azure-functions/functions-bindings-event-grid.md) em vez de função acionada por um WebHook, a validação de ponto final é manipulada para. Se utilizar um serviço da API de terceiros (como [Zapier](https://zapier.com) ou [IFTTT](https://ifttt.com/)), poderá não conseguir recuperar programaticamente o código de validação. Por esses serviços, pode validar manualmente a subscrição ao utilizar um URL de validação que é enviado no evento de validação de subscrição. Copie esse URL no `validationUrl` propriedade e enviar um GET pedido através do seu navegador da web ou um cliente REST.

Validação manual está em pré-visualização. Para a utilizar, tem de instalar a [extensão do Event Grid](/cli/azure/azure-cli-extensions-list) para a [CLI do Azure](/cli/azure/install-azure-cli). Pode instalá-la com `az extension add --name eventgrid`. Se estiver a utilizar a API REST, verifique se está a utilizar `api-version=2018-05-01-preview`.

A recuperação programaticamente o código de validação, utilize o seguinte código (também pode encontrar exemplos relacionados em https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer):

```csharp
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Resposta de validação de teste

Teste a função de resposta de validação ao colar o evento de exemplo para o campo de teste para a função:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Quando clica em execução, o resultado deve ser 200 OK e `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` no corpo:

![resposta de validação](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Processar eventos de armazenamento de BLOBs

Agora, vamos estender a função para processar `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Manipulação de eventos de Blob criado de teste

Testar a nova funcionalidade da função, colocando uma [eventos de armazenamento de BLOBs](./event-schema-blob-storage.md#example-event) para o campo de teste e execução:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Deverá ver a saída de URL do blob no registo de função:

![Log de saída](./media/receive-events/blob-event-response.png)

Também pode testar através da criação de uma conta de armazenamento de BLOBs ou fins gerais V2 conta de armazenamento (GPv2) [subscrição de evento e adicionar](../storage/blobs/storage-blob-event-quickstart.md)e definir o ponto final para o URL da função:

![URL da Função](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Tratar eventos personalizados

Finalmente, permite estender a função mais uma vez, para que ele também pode lidar com eventos personalizados. Adicionar uma verificação para o seu evento `Contoso.Items.ItemReceived`. Seu código final deve ser semelhante:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Manipulação de eventos personalizados de teste

Por fim, de teste que a sua função extented agora pode lidar com o tipo de evento personalizado:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Também pode testar essa funcionalidade em direto por [enviar um evento personalizado com o CURL a partir do Portal](./custom-event-quickstart-portal.md) ou pelo [de lançamentos para um tópico personalizado](./post-to-custom-topic.md) em qualquer serviço ou aplicação que pode publicar para um ponto final, como [Postman](https://www.getpostman.com/). Crie um tópico personalizado e uma subscrição de evento com o ponto final definido como o URL da função.

## <a name="next-steps"></a>Passos Seguintes

* Explorar o [Management da grelha de eventos do Azure e SDKs de publicar](./sdk-overview.md)
* Saiba como [postar um tópico personalizado](./post-to-custom-topic.md)
* Experimente um dos tutoriais Event Grid e funções detalhados como [redimensionamento de imagens carregadas para o armazenamento de BLOBs](resize-images-on-storage-blob-upload-event.md)
