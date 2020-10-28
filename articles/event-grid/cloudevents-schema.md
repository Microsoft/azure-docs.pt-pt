---
title: Use a grelha de eventos Azure com eventos no esquema cloudEvents
description: Descreve como usar o esquema do CloudEvents para eventos em Azure Event Grid. O serviço suporta eventos na implementação json de Eventos cloud.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 51e3f5477cad39b48b441122cf17599f7d25ccf8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747290"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Use esquema cloudEvents v1.0 com grade de eventos
Além do seu [esquema de eventos predefinidos,](event-schema.md)a Azure Event Grid suporta de forma nativa eventos na [implementação JSON de cloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [protocolo HTTP.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum de eventos para a publicação e consumindo eventos baseados na nuvem. Este esquema permite uma ferramenta uniforme, formas padrão de encaminhamento & eventos de manuseamento, e formas universais de desseeciar o esquema do evento exterior. Com um esquema comum, pode integrar mais facilmente o trabalho através das plataformas.

O CloudEvents está a ser construído por [vários colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, através da [Cloud Native Computing Foundation.](https://www.cncf.io/) Atualmente está disponível como versão 1.0.

Este artigo descreve como usar o esquema cloudEvents com grade de eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instale a função de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema cloudEvent

Aqui está um exemplo de um evento de Armazenamento Azure Blob no formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Uma descrição detalhada dos campos disponíveis, seus tipos e definições em CloudEvents v1.0 está [disponível aqui.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

Os valores dos cabeçalhos para os eventos entregues no esquema cloudEvents e no esquema da Grelha de Eventos são os mesmos, exceto `content-type` . Para o esquema cloudEvents, o valor do cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"` . Para o esquema da Grelha de Eventos, o valor do cabeçalho é `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Configure grelha de eventos para eventos cloudEvents

Você pode usar a Grade de Eventos tanto para a entrada como para a saída de eventos no esquema do CloudEvents. Você pode usar Eventos CloudEvents para eventos do sistema, como eventos blob storage e eventos IoT Hub, e eventos personalizados. Também pode transformar esses eventos no fio para trás e para a frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato de grelha de eventos  | Formato CloudEvents
| Formato de grelha de eventos  | Formato de grelha de eventos

Para todos os esquemas de eventos, o Event Grid requer validação ao publicar um tópico de grelha de eventos e ao criar uma subscrição de eventos. Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)

### <a name="input-schema"></a>Esquema de entrada

Define o esquema de entrada para um tópico personalizado quando cria o tópico personalizado.

Para a CLI do Azure, utilize:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Para o PowerShell, utilize:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Esquema de saída

Define o esquema de saída quando cria a subscrição do evento.

Para a CLI do Azure, utilize:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Para o PowerShell, utilize:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Atualmente, não é possível utilizar um gatilho de Grade de Eventos para uma aplicação Azure Functions quando o evento é entregue no esquema do CloudEvents. Utilize um gatilho HTTP. Para, por exemplo, implementar um gatilho HTTP que receba eventos no esquema cloudEvents, consulte [Utilização de Eventos CloudEvents com Funções Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto final com CloudEvents v1.0

Se já está familiarizado com a Grade de Eventos, poderá estar ciente do aperto de mão de validação de ponto final da Event Grid para prevenir abusos. O CloudEvents v1.0 implementa a sua própria [semântica de proteção contra abusos](webhook-event-delivery.md) utilizando o método HTTP OPTIONS. Pode ler mais [aqui.](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) Ao utilizar o esquema cloudEvents para saída, a Grade de Eventos utiliza com a proteção de abuso cloudEvents v1.0 em vez do mecanismo de validação da Grelha de Eventos.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Utilizar com Funções Azure

A [ligação Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) não suporta os Eventos CloudEvents de forma nativa, pelo que as funções acionadas por HTTP são usadas para ler mensagens CloudEvents. Ao utilizar um gatilho HTTP para ler Eventos CloudEvents, tem de escrever código para o que o gatilho da Grelha de Eventos faz automaticamente:

* Envia uma resposta de validação a um [pedido de validação de subscrição.](../event-grid/webhook-event-delivery.md)
* Invoca a função uma vez por elemento da matriz do evento contida no corpo de pedido.

Para obter informações sobre o URL a utilizar para invocar a função localmente ou quando é executado em Azure, consulte a [documentação de referência de ligação do gatilho HTTP](../azure-functions/functions-bindings-http-webhook.md)

O seguinte código C# da amostra para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos.  Use este exemplo para eventos entregues no esquema cloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A amostra seguinte código JavaScript para um gatilho HTTP simula o comportamento do gatilho da Grade de Evento. Use este exemplo para eventos entregues no esquema cloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200 };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Encorajamo-lo a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md) para o CloudEvents.
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
