---
title: Esquema de eventos do Event Grid Service Bus do Azure
description: Descreve as propriedades que são fornecidas para eventos do Service Bus com o Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561766"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Esquema de eventos do Azure Event Grid para o Service Bus

Este artigo fornece as propriedades e o esquema de eventos do Service Bus. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem do evento do Service Bus](event-sources.md#service-bus).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Service Bus emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Desencadeado quando existem mensagens ativas numa fila ou subscrição, sem recetores à escuta. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Desencadeado quando existem mensagens ativas numa fila entregues e não existem serviços de escuta ativos. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema de mensagens ativas com nenhum evento de serviços de escuta:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento de fila entregues é semelhante:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| assunto | string | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| ID | string | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos de armazenamento de Blobs. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| namespaceName | string | O espaço de nomes do Service Bus o recurso existe. |
| requestUri | string | O URI para a fila específica ou a subscrição que emite o evento. |
| entityType | string | O tipo de entidade do Service Bus emite eventos (fila ou subscrição). |
| queueName | string | A fila com mensagens ativas se subscrever uma fila. Valor null, se utilizar tópicos / subscrições. |
| topicName | string | O tópico a subscrição do Service Bus com mensagens ativas pertence. Valor nulo se a utilização de uma fila. |
| subscriptionName | string | A subscrição do Service Bus com mensagens de Active Directory. Valor nulo se a utilização de uma fila. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
* Para obter detalhes sobre como utilizar o Azure Event Grid com o Service Bus, consulte a [Service Bus para descrição geral da integração do Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Tente [a receção de eventos do Service Bus com as funções ou o Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
