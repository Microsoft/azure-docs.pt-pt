---
title: Egress and endpoints - Azure Digital Twins | Microsoft Docs
description: Learn how to create and egress event endpoints in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 95dbed72aeca639041d259e9c92c2a3b73ef63fe
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456926"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Egress and endpoints in Azure Digital Twins

Azure Digital Twins *endpoints* represent a message or event broker within a user's Azure subscription. Events and messages can be sent to Azure Event Hubs, Azure Event Grid, and Azure Service Bus topics.

Events are routed to endpoints according to predefined routing preferences. Users specify which *event types* each endpoint may receive.

To learn more about events, routing, and event types, refer to [Routing events and messages in Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Eventos

Events are sent by IoT objects (such as devices and sensors) for processing by Azure message and event brokers. Events are defined by the following [Azure Event Grid event schema reference](../event-grid/event-schema.md).

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
| ID | string | Unique identifier for the event. |
| subject | string | Publisher-defined path to the event subject. |
| data | objeto | Event data specific to the resource provider. |
| eventType | string | One of the registered event types for this event source. |
| eventTime | string | The time the event is generated based on the provider's UTC time. |
| dataVersion | string | The schema version of the data object. The publisher defines the schema version. |
| metadataVersion | string | The schema version of the event metadata. Event Grid defines the schema of the top-level properties. Event Grid provides this value. |
| tópico | string | Full resource path to the event source. This field isn't writeable. Event Grid provides this value. |

For more information about the Event Grid event schema:

- Review the [Azure Event Grid event schema reference](../event-grid/event-schema.md).
- Read the [Azure EventGrid Node.js SDK EventGridEvent reference](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Event types

Events types classify the nature of the event and are set in the **eventType** field. Available event types are given by the following list:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

The event formats for each event type are further described in the following subsections.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** applies to graph changes. The **subject** property specifies the type of object affected. The following types of objects might trigger this event:

- Dispositivo
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Relatório
- RoleDefinition
- Sensor
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
- UserExtendedProperty

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
| YOUR_TOPIC_NAME | The name of your customized topic |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** is an event sent by a user-defined function (UDF).
  
> [!IMPORTANT]  
> This event must be explicitly sent from the UDF itself.

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
| YOUR_TOPIC_NAME | The name of your customized topic |

### <a name="sensorchange"></a>SensorChange

**SensorChange** is an update to a sensor's state based on telemetry changes.

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
| YOUR_TOPIC_NAME | The name of your customized topic |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** is an update to a space's state based on telemetry changes.

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
| YOUR_TOPIC_NAME | The name of your customized topic |

### <a name="devicemessage"></a>DeviceMessage

By using **DeviceMessage**, you can specify an **EventHub** connection to which raw telemetry events can be routed as well from Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** is combinable only with **EventHub**. You can't combine **DeviceMessage** with any of the other event types.
> - You can specify only one endpoint of the combination of type **EventHub** or **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurar pontos finais

Endpoint management is exercised through the Endpoints API.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

The following examples demonstrate how to configure the supported endpoints.

>[!IMPORTANT]
> Pay careful attention to the **eventTypes** attribute. It defines which event types are handled by the endpoint and thus determine its routing.

An authenticated HTTP POST request against:

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Route to Service Bus event types **SensorChange**, **SpaceChange**, and **TopologyOperation**:

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
    | YOUR_NAMESPACE | The namespace of your endpoint |
    | YOUR_PRIMARY_KEY | The primary connection string used to authenticate |
    | YOUR_SECONDARY_KEY | The secondary connection string used to authenticate |
    | YOUR_TOPIC_NAME | The name of your customized topic |

- Route to Event Grid event types **SensorChange**, **SpaceChange**, and **TopologyOperation**:

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
    | YOUR_PRIMARY_KEY | The primary connection string used to authenticate|
    | YOUR_SECONDARY_KEY | The secondary connection string used to authenticate |
    | YOUR_TOPIC_NAME | The name of your customized topic |

- Route to Event Hubs event types **SensorChange**, **SpaceChange**, and **TopologyOperation**:

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
    | YOUR_NAMESPACE | The namespace of your endpoint |
    | YOUR_PRIMARY_KEY | The primary connection string used to authenticate |
    | YOUR_SECONDARY_KEY | The secondary connection string used to authenticate |
    | YOUR_EVENT_HUB_NAME | The name of your event hub |

- Route to Event Hubs event type **DeviceMessage**. The inclusion of `EntityPath` in the **connectionString** is mandatory:

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
    | YOUR_NAMESPACE | The namespace of your endpoint |
    | YOUR_PRIMARY_KEY | The primary connection string used to authenticate |
    | YOUR_SECONDARY_KEY | The secondary connection string used to authenticate |
    | YOUR_EVENT_HUB_NAME | The name of your event hub |

> [!NOTE]  
> Upon the creation of a new endpoint, it might take up to 5 to 10 minutes to start receiving events at the endpoint.

## <a name="primary-and-secondary-connection-keys"></a>Primary and secondary connection keys

When a primary connection key becomes unauthorized, the system automatically tries the secondary connection key. That provides a backup and allows the possibility to gracefully authenticate and update the primary key through the Endpoints API.

If both the primary and secondary connection keys are unauthorized, the system enters an exponential back-off wait time of up to 30 minutes. Events are dropped on each triggered back-off wait time.

Whenever the system is in a back-off wait state, updating connections keys through the Endpoints API might take up to 30 minutes to take effect.

## <a name="unreachable-endpoints"></a>Unreachable endpoints

When an endpoint becomes unreachable, the system enters an exponential back-off wait time of up to 30 minutes. Events are dropped on each triggered back-off wait time.

## <a name="next-steps"></a>Passos seguintes

- Learn [how to use Azure Digital Twins Swagger](how-to-use-swagger.md).

- Learn more about [routing events and messages](concepts-events-routing.md) in Azure Digital Twins.
