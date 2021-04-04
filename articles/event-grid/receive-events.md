---
title: Receba eventos da Azure Event Grid para um ponto final HTTP
description: Descreve como validar um ponto final HTTP, em seguida, receber e desseializar eventos da Azure Event Grid
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023732"
---
# <a name="receive-events-to-an-http-endpoint"></a>Receber eventos para um ponto final HTTP

Este artigo descreve como [validar um ponto final HTTP](webhook-event-delivery.md) para receber eventos de uma Subscrição de Eventos e, em seguida, receber e desseializar eventos. Este artigo utiliza uma Função Azure para fins de demonstração, no entanto os mesmos conceitos aplicam-se independentemente do local onde a aplicação está hospedada.

> [!NOTE]
> Recomenda-se **vivamente** que utilize um [gatilho de grelha de evento](../azure-functions/functions-bindings-event-grid.md) ao acionar uma função Azure com grelha de evento. O uso de um gatilho genérico webHook aqui é demonstrativo.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma aplicação de função com uma função httpizada.

## <a name="add-dependencies"></a>Adicionar dependências

Se estiver a desenvolver em .NET, [adicione uma dependência](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) à sua função para o pacote `Microsoft.Azure.EventGrid` [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Os exemplos deste artigo requerem a versão 1.4.0 ou posterior.

Os SDKs para outros idiomas estão disponíveis através da referência [Publish SDKs.](./sdk-overview.md#data-plane-sdks) Estes pacotes têm os modelos para tipos de eventos nativos `EventGridEvent` `StorageBlobCreatedEventData` como, e `EventHubCaptureFileCreatedEventData` .

Clique no link "Ver Ficheiros" na sua Função Azure (painel de funções à direita no portal das funções Azure) e crie um ficheiro chamado project.jsligado. Adicione o seguinte conteúdo ao `project.json` ficheiro e guarde-o:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Pacote NuGet adicionado](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validação do ponto final

A primeira coisa que queres fazer é lidar com `Microsoft.EventGrid.SubscriptionValidationEvent` os acontecimentos. Sempre que alguém subscreve um evento, a Event Grid envia um evento de validação para o ponto final com uma `validationCode` na carga de dados. O ponto final é necessário para ecoar isto de volta no organismo de resposta para [provar que o ponto final é válido e propriedade de si](webhook-event-delivery.md). Se estiver a utilizar um [Gatilho de grelha de evento](../azure-functions/functions-bindings-event-grid.md) em vez de uma Função ativada webHook, a validação do ponto final é tratada para si. Se utilizar um serviço API de terceiros (como [Zapier](https://zapier.com/home) ou [IFTTT),](https://ifttt.com/)poderá não conseguir fazer eco programático o código de validação. Para esses serviços, pode validar manualmente a subscrição utilizando um URL de validação que é enviado no evento de validação de subscrição. Copie esse URL na `validationUrl` propriedade e envie um pedido GET através de um cliente REST ou do seu navegador web.

Em C#, a `DeserializeEventGridEvents()` função deserializa os eventos de Grade de Eventos. Deserializa os dados do evento no tipo apropriado, como StorageBlobCreatedEventData. Utilize a `Microsoft.Azure.EventGrid.EventTypes` classe para obter tipos e nomes de eventos suportados.

Para ecoar programáticamente o código de validação, utilize o seguinte código. Pode encontrar amostras relacionadas no [exemplo do Consumidor de Grade de Eventos.](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer)

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
}
```

```javascript
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

### <a name="test-validation-response"></a>Resposta de validação de testes

Testar a função de resposta de validação colando o evento da amostra no campo de ensaio para a função:

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

Quando clicar em Executar, a saída deve ser de 200 OK e `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` no corpo:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Pedido de validação":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Saída de validação":::

## <a name="handle-blob-storage-events"></a>Lidar com eventos de armazenamento Blob

Agora, vamos estender a função para `Microsoft.Storage.BlobCreated` lidar:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
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

### <a name="test-blob-created-event-handling"></a>Teste Blob Criado tratamento de eventos

Teste a nova funcionalidade da função colocando um [evento de armazenamento Blob](./event-schema-blob-storage.md#example-event) no campo de teste e executando:

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

Deve ver a saída de URL blob no registo de funções:

![Registo de saída](./media/receive-events/blob-event-response.png)

Também pode testar criando uma conta de armazenamento Blob ou uma conta de armazenamento de Finalidade Geral V2 (GPv2), [adicionando e subscrição de eventos,](../storage/blobs/storage-blob-event-quickstart.md)e definindo o ponto final para o URL de função:

![URL da Função](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Lidar com eventos personalizados

Finalmente, vamos alargar a função mais uma vez para que possa também lidar com eventos personalizados. 

Em C#, o SDK suporta mapear um nome de tipo de evento para o tipo de dados do evento. Utilize a `AddOrUpdateCustomEventMapping()` função para mapear o evento personalizado.

Adicione um cheque para o seu `Contoso.Items.ItemReceived` evento. O seu código final deve parecer:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

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
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testar o manuseamento de eventos personalizados

Finalmente, teste que a sua função pode agora lidar com o seu tipo de evento personalizado:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Também pode testar esta funcionalidade ao vivo [enviando um evento personalizado com CURL a partir do Portal](./custom-event-quickstart-portal.md) ou [publicando para um tópico personalizado](./post-to-custom-topic.md) usando qualquer serviço ou aplicação que possa POSTar para um ponto final como o [Carteiro.](https://www.getpostman.com/) Crie um tópico personalizado e uma subscrição de eventos com o set de ponto final como URL de função.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Passos seguintes

* Explore a [Azure Event Grid Management e publique SDKs](./sdk-overview.md)
* Saiba como [publicar um tópico personalizado](./post-to-custom-topic.md)
* Experimente um dos tutoriais aprofundados da Grelha de Eventos e Funções, como [redimensionar imagens enviadas para o armazenamento blob](resize-images-on-storage-blob-upload-event.md)
