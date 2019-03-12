---
title: Utilize o Azure Event Grid com eventos no esquema do CloudEvents
description: Descreve como definir o esquema do CloudEvents para eventos no Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: c9c0fd8bf7dc893ce07a6e3ce9cb392fc652a560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535857"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Utilizar o esquema do CloudEvents com o Event Grid

Além seu [esquema de eventos padrão](event-schema.md), Azure Event Grid os eventos no suporta nativamente o [esquema do CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](https://cloudevents.io/) é um [especificação de abrir](https://github.com/cloudevents/spec/blob/master/spec.md) para descrever os dados de eventos.

CloudEvents simplifica a interoperabilidade, fornecendo um esquema de evento comum para publicação e utilizam a nuvem com base em eventos. Esse esquema permite ferramentas uniforme, formas de roteamento e processamento de eventos e universais formas de anular a serialização do esquema de eventos externo. Com um esquema comum, pode integrar mais facilmente o trabalho entre plataformas.

CloudEvents está sendo criado por várias [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo Microsoft, através do [Foundation computação nativa da Cloud](https://www.cncf.io/). Está atualmente disponível como versão 0,1.

Este artigo descreve como utilizar o esquema do CloudEvents com o Event Grid.

## <a name="install-preview-feature"></a>Instale a funcionalidade de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Eis um exemplo de um evento de armazenamento de Blobs do Azure no formato do CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

Do CloudEvents v0.1 tem as seguintes propriedades disponíveis:

| CloudEvents        | Type     | Valor de JSON de exemplo             | Descrição                                                        | Mapeamento do Event Grid
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | Tipo de ocorrência que tenham acontecido                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | A versão do eventType (opcional)                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | A versão da especificação do CloudEvents que utiliza o evento        | *passar por*
| source             | URI      | "/mycontext"                     | Descreve o produtor de eventos                                       | topic#subject
| eventID            | String   | "1234-1234-1234"                 | ID do evento                                                    | id
| eventTime          | Carimbo de data/hora| "2018-04-05T17:31:00Z"           | Timestamp de quando o evento ocorreu (opcional)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | Uma ligação para o esquema que seguem o atributo de dados (opcional) | *não utilizado*
| contentType        | String   | "application/json"               | Descrever o formato de codificação de dados (opcional)                       | *não utilizado*
| Extensões         | Mapa      | { "extA": "vA", "extB", "vB" }  | Quaisquer metadados adicionais (opcional)                                 | *não utilizado*
| dados               | Objeto   | { "objA": "vA", "objB", "vB" }  | O payload do evento (opcional)                                       | dados

Para obter mais informações, consulte a [especificação do CloudEvents](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

Os valores de cabeçalhos para eventos fornecidos no esquema do CloudEvents e o esquema de grelha de eventos são os mesmos, exceto para `content-type`. Para o esquema do CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema do Event Grid, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurar o Event Grid para do CloudEvents

Pode utilizar o Event Grid para entrada e saída de eventos no esquema do CloudEvents. Pode usar do CloudEvents para eventos de sistema, como eventos de armazenamento de BLOBs e eventos do IoT Hub e eventos personalizados. Ele também pode transformar esses eventos na conexão e volta.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato do CloudEvents | Formato do CloudEvents
| Formato de grade do evento  | Formato do CloudEvents
| Formato do CloudEvents | Formato de grade do evento
| Formato de grade do evento  | Formato de grade do evento

Para todos os esquemas de eventos, o Event Grid exige validação quando publicar um tópico do event grid e ao criar uma subscrição de evento. Para obter mais informações, consulte [Event Grid segurança e autenticação](security-authentication.md).

### <a name="input-schema"></a>Esquema de entrada

Definir o esquema de entrada para um tópico personalizado quando cria o tópico personalizado.

Para a CLI do Azure, utilize:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
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
  -InputSchema CloudEventV01Schema
```

A versão atual do CloudEvents não suporta a criação de batches de eventos. Para publicar eventos com o esquema de CloudEvent para um tópico, publicar individualmente a cada evento.

### <a name="output-schema"></a>Esquema de saída

Definir o esquema de saída ao criar a subscrição de evento.

Para a CLI do Azure, utilize:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Para o PowerShell, utilize:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

A versão atual do do CloudEvents não suporta a criação de batches de eventos. Uma subscrição de evento que está configurada para o esquema de CloudEvent recebe individualmente a cada evento. Atualmente, não é possível utilizar um acionador do Event Grid para uma aplicação de funções do Azure quando o evento é entregue no esquema do CloudEvents. Utilize um acionador HTTP. Para obter exemplos de implementação de um acionador HTTP que recebe eventos no esquema do CloudEvents, consulte [utilizar um acionador HTTP como um acionador do Event Grid](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a monitorização de entregas de eventos, consulte [entrega de mensagens do Monitor Event Grid](monitor-event-delivery.md).
* Incentivamos a testar, comentário, e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) do CloudEvents.
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
