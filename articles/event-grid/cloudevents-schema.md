---
title: Use a grelha de eventos Azure com eventos no esquema cloudEvents
description: Descreve como usar o esquema do CloudEvents para eventos em Azure Event Grid. O serviço suporta eventos na implementação json de Eventos CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737907"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Use esquema cloudEvents v1.0 com grade de eventos
Além do seu [esquema de eventos predefinidos,](event-schema.md)a Azure Event Grid suporta de forma nativa eventos na [implementação JSON de cloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [protocolo HTTP.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum de eventos para publicar e consumir eventos baseados na nuvem. Este esquema permite a ferramenta uniforme, formas padrão de encaminhamento e manuseamento de eventos, e formas universais de desseeciação do esquema de eventos exteriores. Com um esquema comum, pode integrar mais facilmente o trabalho através das plataformas.

O CloudEvents está a ser construído por [vários colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, através da [Cloud Native Computing Foundation.](https://www.cncf.io/) Atualmente está disponível como versão 1.0.

Este artigo descreve como usar o esquema cloudEvents com grade de eventos.

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

Para obter uma descrição detalhada dos campos disponíveis, seus tipos e definições, consulte [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para os eventos entregues no esquema cloudEvents e no esquema da Grelha de Eventos são os mesmos, exceto `content-type` . Para o esquema cloudEvents, o valor do cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"` . Para o esquema da Grelha de Eventos, o valor do cabeçalho é `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Configure grelha de eventos para eventos cloudEvents

Pode utilizar a Grade de Eventos para a entrada e saída de eventos no esquema cloudEvents. A tabela a seguir descreve as possíveis transformações:

 Recurso de grelha de eventos | Esquema de entrada       | Esquema de entrega
|---------------------|-------------------|---------------------
| Tópicos do Sistema       | Esquema do Event Grid | Esquema de grelha de evento ou esquema de CloudEvents
| Tópicos/Domínios do Utilizador | Esquema do Event Grid | Esquema de grelha de evento ou esquema de CloudEvents
| Tópicos/Domínios do Utilizador | Esquema cloudEvents | Esquema cloudEvents
| Tópicos/Domínios do Utilizador | Esquema personalizado     | Esquema personalizado, esquema de grelha de evento ou esquema de CloudEvents
| PartnerTopics       | Esquema cloudEvents | Esquema cloudEvents

Para todos os esquemas de eventos, o Event Grid requer validação quando está a publicar um tópico de Grade de Eventos e quando está a criar uma subscrição de eventos.

Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)

### <a name="input-schema"></a>Esquema de entrada

Define o esquema de entrada para um tópico personalizado quando cria o tópico personalizado.

Para o Azure CLI, utilize:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Para o PowerShell, utilize:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Esquema de saída

Define o esquema de saída quando cria a subscrição do evento.

Para o Azure CLI, utilize:

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
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Atualmente, não é possível utilizar um gatilho de Grade de Eventos para uma aplicação Azure Functions quando o evento é entregue no esquema do CloudEvents. Utilize um gatilho HTTP. Para, por exemplo, implementar um gatilho HTTP que receba eventos no esquema cloudEvents, consulte [Utilização de Eventos CloudEvents com Funções Azure](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto final com CloudEvents v1.0

Se já está familiarizado com a Grade de Eventos, poderá estar ciente do aperto de mão de validação de ponto final para prevenir abusos. O CloudEvents v1.0 implementa a sua própria [semântica de proteção](webhook-event-delivery.md) contra abusos utilizando o método HTTP OPTIONS. Para ler mais sobre o mesmo, consulte [HTTP 1.1 Web Hooks para entrega de eventos - Versão 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando utiliza o esquema cloudEvents para saída, a Grade de Eventos utiliza a proteção contra abusos CloudEvents v1.0 em vez do mecanismo de validação da Grelha de Eventos.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Utilizar com Funções Azure

A [ligação Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) não suporta os Eventos CloudEvents de forma nativa, pelo que as funções desencadeadas por HTTP são usadas para ler mensagens CloudEvents. Quando utilizar um gatilho HTTP para ler Eventos CloudEvents, tem de escrever código para o que o gatilho da Grelha de Eventos faz automaticamente:

* Envia uma resposta de validação a um [pedido de validação de subscrição](../event-grid/webhook-event-delivery.md)
* Invoca a função uma vez por elemento da matriz do evento contida no corpo de pedido

Para obter informações sobre o URL a utilizar para invocar a função localmente ou quando funciona em Azure, consulte a [documentação de referência de ligação do gatilho HTTP](../azure-functions/functions-bindings-http-webhook.md).

O seguinte código C# da amostra para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos. Use este exemplo para eventos entregues no esquema cloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
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
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
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
* Encorajamo-lo a testar, comentar e [contribuir para o CloudEvents.](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
