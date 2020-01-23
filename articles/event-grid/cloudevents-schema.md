---
title: Usar a grade de eventos do Azure com eventos no esquema CloudEvents
description: Descreve como usar o esquema CloudEvents para eventos na grade de eventos do Azure. O serviço oferece suporte a eventos na implementação JSON de eventos de nuvem.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: e7cddf95a6537e5799acc523effb484c2249453d
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548055"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Usar o esquema do CloudEvents v 1.0 com a grade de eventos

Além de seu [esquema de evento padrão](event-schema.md), a grade de eventos do Azure oferece suporte nativo a eventos na [implementação JSON do CloudEvents v 1.0 e da Associação de](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) [protocolo http](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever os dados do evento.

O CloudEvents simplifica a interoperabilidade fornecendo um esquema de evento comum para publicação e consumo de eventos baseados em nuvem. Esse esquema permite ferramentas uniformes, maneiras padrão de roteamento & manipulação de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente o trabalho entre as plataformas.

A CloudEvents está sendo criada por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [nuvem Native Computing Foundation](https://www.cncf.io/). Ele está disponível atualmente como a versão 1,0.

Este artigo descreve como usar o esquema CloudEvents com a grade de eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instale a funcionalidade de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema CloudEvent

Aqui está um exemplo de um evento de armazenamento de BLOBs do Azure no formato CloudEvents:

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

Uma descrição detalhada dos campos disponíveis, seus tipos e definições no CloudEvents v 1.0 está [disponível aqui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores de cabeçalhos para os eventos entregues no esquema CloudEvents e no esquema da grade de eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema de grade de eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurar a grade de eventos para CloudEvents

Você pode usar a grade de eventos para entrada e saída de eventos no esquema CloudEvents. Você pode usar o CloudEvents para eventos do sistema, como eventos de armazenamento de BLOBs e eventos de Hub IoT e eventos personalizados. Ele também pode transformar esses eventos na conexão.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato da grade de eventos  | Formato CloudEvents
| Formato CloudEvents | Formato da grade de eventos
| Formato da grade de eventos  | Formato da grade de eventos

Para todos os esquemas de evento, a grade de eventos requer validação ao publicar em um tópico da grade de eventos e ao criar uma assinatura de evento. Para obter mais informações, consulte [Event Grid segurança e autenticação](security-authentication.md).

### <a name="input-schema"></a>Esquema de entrada

Você define o esquema de entrada para um tópico personalizado ao criar o tópico personalizado.

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

A versão atual do CloudEvents não dá suporte ao processamento em lote de eventos. Para publicar eventos com o esquema CloudEvent em um tópico, publique cada evento individualmente.

### <a name="output-schema"></a>Esquema de saída

Você define o esquema de saída ao criar a assinatura de evento.

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

 No momento, você não pode usar um gatilho de grade de eventos para um aplicativo Azure Functions quando o evento é entregue no esquema CloudEvents. Use um gatilho HTTP. Para obter exemplos de como implementar um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte [usando CloudEvents com Azure Functions](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto de extremidade com CloudEvents v 1.0

Se você já estiver familiarizado com a grade de eventos, talvez esteja ciente do handshake de validação do ponto de extremidade da grade de eventos para evitar abusos. O CloudEvents v 1.0 implementa sua própria [semântica de proteção de abuso](security-authentication.md#webhook-event-delivery) usando o método de opções http. Pode ler mais sobre o assunto [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao usar o esquema CloudEvents para saída, a grade de eventos usa com a proteção de abuso do CloudEvents v 1.0 em vez do mecanismo de evento de validação da grade de eventos.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Usar com Azure Functions

A [associação da grade de eventos Azure Functions](../azure-functions/functions-bindings-event-grid.md) não dá suporte nativo a CloudEvents, portanto as funções disparadas por http são usadas para ler mensagens CloudEvents. Ao usar um gatilho HTTP para ler CloudEvents, você precisa escrever código para o que o gatilho de grade de eventos faz automaticamente:

* Envia uma resposta de validação para uma [solicitação de validação de assinatura](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoca a função uma vez por elemento da matriz de eventos contida no corpo da solicitação.

Para obter informações sobre a URL a ser usada para invocar a função localmente ou quando ela é executada no Azure, consulte a [documentação de referência de associação de gatilho http](../azure-functions/functions-bindings-http-webhook.md)

O código de C# exemplo a seguir para um gatilho http simula o comportamento do gatilho de grade de eventos.  Use este exemplo para eventos entregues no esquema CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código JavaScript de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho de grade de eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
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

* Para obter informações sobre como monitorar entregas de eventos, consulte [monitorar a entrega de mensagens na grade de eventos](monitor-event-delivery.md).
* Incentivamos você a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) com o CloudEvents.
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
