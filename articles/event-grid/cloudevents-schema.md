---
title: Use a Grelha de Eventos Azure com eventos em esquema CloudEvents
description: Descreve como usar o esquema CloudEvents para eventos em Azure Event Grid. O serviço suporta eventos na implementação da JSON de Cloud Events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 2e4de91034de0d036cd99e265949ba85a5939180
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629332"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Use CloudEvents v1.0 schema com grelha de eventos
Além do seu [esquema de evento padrão,](event-schema.md)a Azure Event Grid apoia de forma nativa eventos na [implementação jSON de CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [http protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados do evento.

A CloudEvents simplifica a interoperabilidade fornecendo um esquema comum de eventos para a publicação e consumindo eventos baseados em nuvem. Este esquema permite uma ferramenta uniforme, formas padrão de encaminhamento & lidar com eventos, e formas universais de desserialização do esquema de eventos exteriores. Com um esquema comum, pode integrar mais facilmente o trabalho em todas as plataformas.

A CloudEvents está a ser construída por [vários colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, através da [Cloud Native Computing Foundation](https://www.cncf.io/). Atualmente está disponível como versão 1.0.

Este artigo descreve como usar o esquema CloudEvents com a Grelha de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar função de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema cloudEvent

Aqui está um exemplo de um evento de armazenamento De Blob Azure no formato CloudEvents:

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

Uma descrição detalhada dos campos disponíveis, seus tipos e definições em CloudEvents v1.0 está [disponível aqui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e `content-type`no esquema da Grelha de Eventos são os mesmos exceto para . Para o esquema CloudEvents, o `"content-type":"application/cloudevents+json; charset=utf-8"`valor do cabeçalho é . Para o esquema da Grelha de `"content-type":"application/json; charset=utf-8"`Eventos, o valor do cabeçalho é .

## <a name="configure-event-grid-for-cloudevents"></a>Configure grelha de eventos para CloudEvents

Pode utilizar a Grelha de Eventos tanto para a entrada como para a saída de eventos em esquema CloudEvents. Você pode usar CloudEvents para eventos de sistema, como eventos de armazenamento blob e eventos IoT Hub, e eventos personalizados. Também pode transformar esses eventos no fio de um lado para o outro.


| Input schema       | Esquema de saída
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato Da Grelha de Eventos  | Formato CloudEvents
| Formato Da Grelha de Eventos  | Formato Da Grelha de Eventos

Para todos os eventos, a Grelha de Eventos requer validação ao publicar para um tópico de grelha de eventos e ao criar uma subscrição de eventos. Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)

### <a name="input-schema"></a>Input schema

Você define o esquema de entrada para um tópico personalizado quando você cria o tópico personalizado.

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

Você define o esquema de saída quando cria a subscrição do evento.

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

 Atualmente, não é possível utilizar um gatilho da Grelha de Eventos para uma aplicação De Funções Azure quando o evento é entregue no esquema CloudEvents. Utilize um gatilho HTTP. Por exemplo, implementando um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte A Utilização de [CloudEvents com Funções Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação do ponto final com CloudEvents v1.0

Se já está familiarizado com a Grelha de Eventos, pode estar ciente do aperto de mão de validação de ponto final da Event Grid para prevenir abusos. CloudEvents v1.0 implementa a sua própria [semântica](webhook-event-delivery.md) de proteção contra abusos utilizando o método HTTP OPTIONS. Pode ler mais sobre o assunto [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao utilizar o esquema CloudEvents para saída, a Rede de Eventos utiliza com a proteção de abuso CloudEvents v1.0 em vez do mecanismo de eventode validação da Rede de Eventos.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Utilização com funções Azure

A [ligação da Grelha de Eventos de Funções Azure](../azure-functions/functions-bindings-event-grid.md) não suporta de forma nativa cloudEvents, pelo que as funções desencadeadas por HTTP são usadas para ler mensagens CloudEvents. Ao utilizar um gatilho HTTP para ler CloudEvents, tem de escrever código para o que o gatilho da Grelha de Eventos faz automaticamente:

* Envia uma resposta de validação a um pedido de [validação](../event-grid/webhook-event-delivery.md)de assinaturas.
* Invoca a função uma vez por elemento da matriz do evento contida no organismo de pedido.

Para obter informações sobre o URL a utilizar para invocar a função localmente ou quando funciona em Azure, consulte a documentação de [referência de ligação http trigger](../azure-functions/functions-bindings-http-webhook.md)

O seguinte código C# da amostra para um gatilho HTTP simula o comportamento do gatilho da Grelha de Eventos.  Use este exemplo para eventos entregues no esquema CloudEvents.

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

A amostra a seguir código JavaScript para um gatilho HTTP simula o comportamento do gatilho da Grelha de Eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
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

* Para obter informações sobre a monitorização das entregas de eventos, consulte a entrega de [mensagens Monitor Event Grid](monitor-event-delivery.md).
* Encorajamo-lo a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) para a CloudEvents.
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
