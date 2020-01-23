---
title: Saída e pontos de extremidade – Azure digital gêmeos | Microsoft Docs
description: Saiba como criar e saída de pontos de extremidade de evento no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511574"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Saída e pontos de extremidade no Azure digital gêmeos

Os pontos de *extremidade* do Azure digital gêmeos representam uma mensagem ou um agente de evento dentro de uma assinatura do Azure do usuário. Eventos e mensagens podem ser enviados para os tópicos hubs de eventos do Azure, grade de eventos do Azure e barramento de serviço do Azure.

Os eventos são roteados para pontos de extremidade de acordo com as preferências de roteamento predefinidas. Os usuários especificam quais *tipos de eventos* cada ponto de extremidade pode receber.

Para saber mais sobre eventos, roteamento e tipos de evento, consulte [roteando eventos e mensagens no Azure digital gêmeos](./concepts-events-routing.md).

## <a name="events"></a>Eventos

Os eventos são enviados por objetos IoT (como dispositivos e sensores) para processamento por mensagens do Azure e agentes de evento. Os eventos são definidos pela seguinte [referência do esquema de eventos da grade de eventos do Azure](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| ID | string | Identificador exclusivo do evento. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| data | objeto | Dados de evento específicos para o provedor de recursos. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. O Event Grid fornece este valor. |

Para obter mais informações sobre o esquema de evento da grade de eventos:

- Examine a [referência do esquema de eventos da grade de eventos do Azure](../event-grid/event-schema.md).
- Leia a [referência EventGridEvent do SDK do node. js do Azure EventGrid](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Tipos de evento

Os tipos de eventos classificam a natureza do evento e são definidos no campo **EventType** . Os tipos de evento disponíveis são fornecidos pela lista a seguir:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Os formatos de evento para cada tipo de evento são descritos mais detalhadamente nas subseções a seguir.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** se aplica a alterações de gráfico. A propriedade **Subject** especifica o tipo de objeto afetado. Os seguintes tipos de objetos podem disparar este evento:

- Dispositivo
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- Repositório
- Relatório
- RoleDefinition
- Sensores
- SensorBlobMetadata
- SensorExtendedProperty
- Espaço
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Sistema
- Utilizador
- UserBlobMetadata
- Extended

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** é um evento enviado por uma UDF (função definida pelo usuário).
  
> [!IMPORTANT]  
> Esse evento deve ser enviado explicitamente do próprio UDF.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="sensorchange"></a>SensorChange

**SensorChange** é uma atualização para o estado de um sensor com base nas alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** é uma atualização para o estado de um espaço com base nas alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="devicemessage"></a>DeviceMessage

Usando o **DeviceMessage**, você pode especificar uma conexão do **EventHub** para a qual os eventos brutos de telemetria também podem ser roteados do Azure digital gêmeos.

> [!NOTE]
> - **DeviceMessage** é combinável somente com o **EventHub**. Não é possível combinar **DeviceMessage** com nenhum dos outros tipos de evento.
> - Você pode especificar apenas um ponto de extremidade da combinação do tipo **EventHub** ou **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurar pontos finais

O gerenciamento de ponto de extremidade é exercido por meio da API de pontos de extremidades.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Os exemplos a seguir demonstram como configurar os pontos de extremidade com suporte.

>[!IMPORTANT]
> Preste muita atenção ao atributo **EventTypes** . Ele define quais tipos de eventos são manipulados pelo ponto de extremidade e, portanto, determinam seu roteamento.

Uma solicitação HTTP POST autenticada em relação a:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Rotear para tipos de evento do barramento de serviço **SensorChange**, **SpaceChange**e **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rotear para tipos de evento da grade de eventos **SensorChange**, **SpaceChange**e **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar|
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rotear para tipos de evento de hubs de eventos **SensorChange**, **SpaceChange**e **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do hub de eventos |

- Rota para o tipo de evento de hubs de eventos **DeviceMessage**. A inclusão de `EntityPath` em **ConnectionString** é obrigatória:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Valor | Substituir |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do hub de eventos |

> [!NOTE]  
> Após a criação de um novo ponto de extremidade, pode levar até 5 a 10 minutos para começar a receber eventos no ponto de extremidade.

## <a name="primary-and-secondary-connection-keys"></a>Chaves de conexão primária e secundária

Quando uma chave de conexão primária se torna não autorizada, o sistema tenta automaticamente a chave de conexão secundária. Isso fornece um backup e permite a possibilidade de autenticar e atualizar normalmente a chave primária por meio da API de pontos de extremidade.

Se as chaves de conexão primária e secundária não forem autorizadas, o sistema entrará em um tempo de espera de retirada exponencial de até 30 minutos. Os eventos são descartados em cada tempo de espera de retirada disparada.

Sempre que o sistema estiver em um estado de espera de retirada, atualizar as chaves de conexão por meio da API de pontos de extremidade pode levar até 30 minutos para entrar em vigor.

## <a name="unreachable-endpoints"></a>Pontos de extremidade inacessíveis

Quando um ponto de extremidade fica inacessível, o sistema entra em um tempo de espera de retirada exponencial de até 30 minutos. Os eventos são descartados em cada tempo de espera de retirada disparada.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como usar o Azure digital gêmeos Swagger](how-to-use-swagger.md).

- Saiba mais sobre como [rotear eventos e mensagens](concepts-events-routing.md) no gêmeos digital do Azure.
