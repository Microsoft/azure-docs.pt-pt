---
title: REST API - Azure Event Grid IoT Edge / Microsoft Docs
description: REST API na Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171759"
---
# <a name="rest-api"></a>API REST
Este artigo descreve as APIs REST da Azure Event Grid em IoT Edge

## <a name="common-api-behavior"></a>Comportamento comum da API

### <a name="base-url"></a>URL Base
A Grelha de Eventos no IoT Edge tem as seguintes APIs expostas sobre HTTP (porta 5888) e HTTPS (porta 4438).

* URL de base para HTTP: http://eventgridmodule:5888
* URL de base para HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Cadeia de consulta de pedido
Todos os pedidos da API requerem o seguinte parâmetro de cadeia de consulta:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Solicitar tipo de conteúdo
Todos os pedidos da API devem ter um **Tipo de Conteúdo.**

No caso de **EventGridSchema** ou **CustomSchema,** o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Em caso de **CloudEventSchemaV1_0** em modo estruturado, o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Em caso de **CloudEventSchemaV1_0** em modo binário, consulte a [documentação](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) para obter mais detalhes.

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

### <a name="put-topic-create--update"></a>Colocar tópico (criar/ atualizar)

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

### <a name="get-topic"></a>Obtenha tópico

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

### <a name="get-all-topics"></a>Obtenha todos os tópicos

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

### <a name="delete-topic"></a>Eliminar tópico

**Pedido**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200, carga útil vazia

## <a name="manage-event-subscriptions"></a>Gerir subscrições de eventos
As amostras desta secção `EndpointType=Webhook;` utilizam. As amostras de json `EndpointType=EdgeHub / EndpointType=EventGrid` estão na próxima secção. 

### <a name="put-event-subscription-create--update"></a>Coloque a subscrição do evento (criar/atualizar)

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


### <a name="get-event-subscription"></a>Obtenha a subscrição do evento

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

### <a name="send-batch-of-events-in-event-grid-schema"></a>Enviar lote de eventos (em esquema de grelha de evento)

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
- ```Id``` é obrigatório. Pode ser qualquer valor de corda que seja povoado pelo chamador. A Grade de EventoS NÃO faz qualquer deteção duplicada ou impõe qualquer semântica neste campo.
- ```Topic``` é opcional, mas se especificado deve corresponder ao topic_name do URL de pedido
- ```Subject``` é obrigatório, pode ser qualquer valor de cadeia
- ```EventType``` é obrigatório, pode ser qualquer valor de cadeia
- ```EventTime``` é obrigatório, não é validado, mas deve ser um DateTime adequado.
- ```DataVersion``` é obrigatório
- ```MetadataVersion``` é opcional, se especificado DEVE ser uma cadeia com o valor ```"1"```
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


**Restrições de carga útil**
- Deve ser uma variedade de eventos.
- Cada entrada de matriz DEVE ser um objeto JSON.
- Sem outros constrangimentos (para além do tamanho da carga útil).

## <a name="examples"></a>Exemplos

### <a name="set-up-topic-with-eventgrid-schema"></a>Configurar o tópico com o esquema EventGrid
Cria um tópico para exigir que os eventos sejam publicados em **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Configurar o tópico com o esquema personalizado
Cria um tópico para exigir que os eventos sejam publicados em `customschema` .

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
Cria um tópico para exigir que os eventos sejam publicados em `cloudeventschema` .

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
Utilize este tipo de destino para enviar eventos para qualquer outro módulo (que acolhe um ponto final HTTP) ou para qualquer ponto final httpable na rede/internet.

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

Constrangimentos no `endpointUrl` atributo:
- Deve não ser nulo.
- Deve ser uma URL absoluta.
- Se outbound__webhook__httpsOnly estiver definido para ser verdadeiro nas definições de EventGridModule, deve ser apenas HTTPS.
- Se outbound__webhook__httpsOnly definido como falso, pode ser HTTP ou HTTPS.

Constrangimentos na `eventDeliverySchema` propriedade:
- Deve coincidir com o esquema de entrada do tópico de subscrição.
- Pode ser nulo. Está em incumprimento do esquema de entrada do tópico.

### <a name="set-up-iot-edge-as-destination"></a>Configurar ioT Edge como destino

Utilize este destino para enviar eventos para o IoT Edge Hub e seja submetido ao subsistema de encaminhamento/filtragem/encaminhamento/encaminhamento do edge hub.

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

### <a name="set-up-event-grid-cloud-as-destination"></a>Configurar a Nuvem de Grelha de Evento como destino

Utilize este destino para enviar eventos para a Grade de Eventos na nuvem (Azure). Primeiro terá de configurar um tópico de utilizador na nuvem para onde os eventos devem ser enviados, antes de criar uma subscrição de eventos no limite.

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
- Deve não ser nulo.
- Deve ser uma URL absoluta.
- O caminho `/api/events` deve ser definido no caminho URL de pedido.
- Deve ter `api-version=2018-01-01` na cadeia de consulta.
- Se outbound__eventgrid__httpsOnly estiver definido como verdadeiro nas definições de EventGridModule (verdadeiro por padrão), deve ser apenas HTTPS.
- Se outbound__eventgrid__httpsOnly estiver definido como falso, pode ser HTTP ou HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames for definido como falso (falso por defeito), deve visar um dos seguintes pontos finais:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve não ser nulo.

Nome tópico:
- Se o Evento Entregar.Schema estiver definido para EventGridSchema, o valor deste campo é colocado no campo Tópico de cada evento antes de ser encaminhado para a Grade de Eventos na nuvem.
- Se o Subscription.EventDeliverySchema estiver definido para CustomEventSchema, esta propriedade é ignorada e a carga útil do evento personalizado é reencaminhada exatamente como foi recebida.

## <a name="set-up-event-hubs-as-a-destination"></a>Configurar os Centros de Eventos como destino

Para publicar num Centro de Eventos, desemote o `endpointType` e `eventHub` forneça:

* ligaçãoStragem: Cadeia de ligação para o centro de eventos específico que está a direcionar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Pode gerar uma cadeia de conexão específica da entidade navegando para o Centro de Eventos específico que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

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

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configurar as filas de autocarros de serviço como destino

Para publicar numa fila de autocarros de serviço, desemote `endpointType` o `serviceBusQueue` toe e forneça:

* ligaçãoStragem: Cadeia de ligação para a fila específica do autocarro de serviço que está a direcionar gerada através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando para a fila específica do Service Bus que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

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

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configurar tópicos de ônibus de serviço como destino

Para publicar num Tópico de Autocarro de Serviço, desemote o `endpointType` tema `serviceBusTopic` e forneça:

* conexãoStragem: Cadeia de ligação para o tópico específico do ônibus de serviço que está a direcionar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando para o tópico específico do Service Bus que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

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

## <a name="set-up-storage-queues-as-a-destination"></a>Configurar as filas de armazenamento como destino

Para publicar numa fila de armazenamento, desemote o  `endpointType` e `storageQueue` forneça:

* nome da fila de armazenamento a que vai publicar.
* ligaçãoStragem: Cadeia de ligação para a conta de armazenamento em que a fila de armazenamento está dentro.

    >[!NOTE]
    > Unline Event Hubs, Service Bus Queues e Service Bus Topics, a cadeia de ligação utilizada para as filas de armazenamento não é específica da entidade. Em vez disso, deve ser apenas o fio de ligação para a Conta de Armazenamento.

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