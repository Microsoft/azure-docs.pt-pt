---
title: Adicionar dados personalizados a eventos em Azure Event Hubs
description: Este artigo mostra-lhe como adicionar dados personalizados a eventos em Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104893480"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Adicionar dados personalizados a eventos em Azure Event Hubs
Uma vez que um evento consiste principalmente de um conjunto opaco de bytes, pode ser difícil para os consumidores desses eventos tomar decisões informadas sobre como processá-los. Para permitir que os editores de eventos ofereçam um melhor contexto para os consumidores, os eventos podem também conter metadados personalizados, sob a forma de um conjunto de pares de valores-chave. Um cenário comum para a inclusão de metadados é fornecer uma dica sobre o tipo de dados contidos por um evento, de modo a que os consumidores compreendam o seu formato e possam dessericializá-lo adequadamente.

> [!NOTE]
> Estes metadados não são utilizados pelo serviço Event Hubs ou de forma significativa; só existe para a coordenação entre editores de eventos e consumidores. 

As secções seguintes mostram-lhe como adicionar dados personalizados a eventos em diferentes linguagens de programação. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Para obter a amostra de código completa, consulte [eventos de publicação com metadados personalizados.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata)

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Para obter a amostra de código completa, consulte [publicar eventos com metadados personalizados.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java)


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Para obter a amostra de código completa, consulte [enviar o lote de dados do evento com propriedades](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Passos seguintes
Consulte as seguintes entradas e amostras rápidas. 

- Quickstarts: [.NET,](event-hubs-dotnet-standard-getstarted-send.md) [Java,](event-hubs-java-get-started-send.md) [Python,](event-hubs-python-get-started-send.md) [JavaScript](event-hubs-node-get-started-send.md)
- Amostras em GitHub: [.NET,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples) [Java,](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples) [Python,](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples) [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
