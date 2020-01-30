---
title: REST API - Azure Event Grid IoT Edge  Microsoft Docs
description: REST API na grelha de eventos na Borda IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841834"
---
# <a name="rest-api"></a>API REST
Este artigo descreve as APIs REST da Grelha de Eventos Azure na Borda IoT

## <a name="common-api-behavior"></a>Comportamento comum da API

### <a name="base-url"></a>Base URL
A Grelha de Eventos na Borda IoT tem as seguintes APIs expostas sobre HTTP (porta 5888) e HTTPS (porta 4438).

* URL base para HTTP: http://eventgridmodule:5888
* URL base para HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Solicitar corda de consulta
Todos os pedidos da API requerem o seguinte parâmetro de corda de consulta:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Solicitar tipo de conteúdo
Todos os pedidos de API devem ter um **Tipo de Conteúdo**.

No caso de **EventGridSchema** ou **CustomSchema,** o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

No caso de **CloudEventSchemaV1_0** em modo estruturado, o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Em caso de **CloudEventSchemaV1_0** em modo binário, consulte a [documentação](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) para mais detalhes.

### <a name="error-response"></a>Resposta de erro
Todas as APIs devolvem um erro com a seguinte carga útil:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Gerir tópicos

### <a name="put-topic-create--update"></a>Colocar tópico (criar /atualizar)

**Pedido**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Carga útil:**

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Resposta**: HTTP 200

**Carga útil:**

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Obter tópico

**Pedido**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Obter todos os tópicos

**Pedido**: ``` GET /topics?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Apagar tópico

**Pedido**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200, carga útil vazia

## <a name="manage-event-subscriptions"></a>Gerir subscrições de eventos
As amostras desta secção utilizam `EndpointType=Webhook;`. As amostras de json para `EndpointType=EdgeHub / EndpointType=EventGrid` estão na próxima secção. 

### <a name="put-event-subscription-create--update"></a>Colocar subscrição de evento (criar /atualizar)

**Pedido**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Carga útil:**
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Resposta**: HTTP 200

**Carga útil:**

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Obtenha subscrição de eventos

**Pedido**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Obtenha subscrições de eventos

**Pedido**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Eliminar subscrição de eventos

**Pedido**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200, sem carga útil


## <a name="publish-events-api"></a>Publicar eventos API

### <a name="send-batch-of-events-in-event-grid-schema"></a>Enviar lote de eventos (em event grid schema)

**Pedido**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Resposta**: HTTP 200, carga útil vazia


**Descrições do campo de carga útil**
- ```Id``` é obrigatório. Pode ser qualquer valor de corda que seja povoado pelo chamador. A Grelha de Eventos NÃO faz qualquer deteção duplicada ou aplica qualquer semântica neste campo.
- ```Topic``` é opcional, mas se especificado deve corresponder ao topic_name do URL de pedido
- ```Subject``` é obrigatório, pode ser qualquer valor de cadeia
- ```EventType``` é obrigatório, pode ser qualquer valor de cadeia
- ```EventTime``` é obrigatório, não é validado, mas deve ser um DataTime adequado.
- ```DataVersion``` é obrigatório
- ```MetadataVersion``` é opcional, se especificado deve ser uma cadeia com o valor ```"1"```
- ```Data``` é opcional, e pode ser qualquer símbolo JSON (número, corda, boolean, matriz, objeto)

### <a name="send-batch-of-events-in-custom-schema"></a>Enviar lote de eventos (em esquema personalizado)

**Pedido**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Resposta**: HTTP 200, carga útil vazia


**Restrições à carga útil**
- Deve ser uma série de eventos.
- Cada entrada de matriz DEVE ser um objeto JSON.
- Não há outros constrangimentos (além do tamanho da carga útil).

## <a name="examples"></a>Exemplos

### <a name="set-up-topic-with-eventgrid-schema"></a>Configurar tópico com eventGrid schema
Estabelece um tópico para exigir que os eventos sejam publicados em **eventgridschema.**

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Configurar tópico com esquema personalizado
Estabelece um tópico para exigir que os eventos sejam publicados em `customschema`.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Configurar tópico com esquema de evento em nuvem
Estabelece um tópico para exigir que os eventos sejam publicados em `cloudeventschema`.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Configurar webHook como destino, eventos a serem entregues em eventgridschema
Utilize este tipo de destino para enviar eventos para qualquer outro módulo (que acolhe um ponto final HTTP) ou para qualquer ponto final http endereçado na rede/internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Constrangimentos no atributo `endpointUrl`:
- Deve ser não-nulo.
- Deve ser uma URL absoluta.
- Se outbound__webhook__httpsOnly for definido de forma verdadeira nas definições do EventGridModule, deve ser apenas HTTPS.
- Se outbound__webhook__httpsOnly definido para falso, pode ser HTTP ou HTTPS.

Constrangimentos na propriedade `eventDeliverySchema`:
- Deve coincidir com o esquema de entrada do tópico de subscrição.
- Pode ser nulo. Não se aplica ao esquema de entrada do tópico.

### <a name="set-up-iot-edge-as-destination"></a>Configurar ioT Edge como destino

Utilize este destino para enviar eventos para o IoT Edge Hub e ser submetido ao subsistema de encaminhamento/filtragem/encaminhamento/encaminhamento/encaminhamento do edge hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Configurar a Nuvem de Grelha de Eventos como destino

Utilize este destino para enviar eventos para a Rede de Eventos na nuvem (Azure). Você precisará primeiro configurar um tópico de utilizador na nuvem para o qual os eventos devem ser enviados, antes de criar uma subscrição de evento no limite.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- Deve ser não-nulo.
- Deve ser uma URL absoluta.
- O caminho `/api/events` deve ser definido no caminho URL do pedido.
- Deve ter `api-version=2018-01-01` na corda de consulta.
- Se outbound__eventgrid__httpsOnly for definido para ser verdade nas definições do EventGridModule (verdadeira por defeito), deve ser apenas HTTPS.
- Se outbound__eventgrid__httpsOnly for configurado como falso, pode ser HTTP ou HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames for definido como falso (falso por defeito), deve visar um dos seguintes pontos finais:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve ser não-nulo.

Nome tópico:
- Se o Subscription.EventDeliverySchema estiver definido para EventGridSchema, o valor deste campo é colocado no campo Tópico de cada evento antes de ser encaminhado para a Rede de Eventos na nuvem.
- Se a Subscrição.EventDeliverySchema está definida para CustomEventSchema, esta propriedade é ignorada e a carga de evento personalizada é reencaminhada exatamente como foi recebida.

## <a name="set-up-event-hubs-as-a-destination"></a>Configurar Centros de Eventos como destino

Para publicar num Event Hub, detete a `endpointType` para `eventHub` e fornecer:

* conexãoString: Linha de ligação para o Hub de Evento específico que você está a visar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Você pode gerar uma cadeia de conexão específica de entidade navegando para o Hub de Evento específico que você gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configurar as filas de ônibus de serviço como um destino

Para publicar numa fila de ônibus de serviço, detete a `endpointType` para `serviceBusQueue` e fornecer:

* ligaçãoString: Linha de ligação para a fila específica do ônibus de serviço que você está a visar gerada através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Gere uma cadeia de ligação específica para a entidade navegando para a fila específica de ônibus de serviço que gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configurar tópicos de ônibus de serviço como um destino

Para publicar num Tópico de Autocarro de Serviço, detete a `endpointType` para `serviceBusTopic` e fornecer:

* conexãoString: Linha de ligação para o tópico específico do ônibus de serviço que você está a visar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Gere uma cadeia de ligação específica para uma entidade navegando para o tópico específico do bus de serviço que gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Configurar filas de armazenamento como destino

Para publicar numa fila de armazenamento, delineie a `endpointType` para `storageQueue` e fornecer:

* nome da fila de armazenamento para a qual está a publicar.
* ligaçãoString: Fio de ligação para a conta de armazenamento em que a fila de armazenamento está dentro.

    >[!NOTE]
    > Unline Event Hubs, Service Bus Queues e Service Bus Topics, a cadeia de ligação utilizada para filas de armazenamento não é específica da entidade. Em vez disso, deve apenas a cadeia de ligação para a Conta de Armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```