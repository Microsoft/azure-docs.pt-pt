---
title: Esquema de evento de ônibus de rede de eventos de rede de eventos de evento seletiva de eventos de eventos de eventos de evento
description: Descreve as propriedades que são fornecidas para eventos de ônibus de serviço com rede de eventos Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561766"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Esquema de evento da Azure Event Grid para ônibus de serviço

Este artigo fornece as propriedades e esquemas para eventos de ônibus de serviço.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

Para obter uma lista de scripts e tutoriais de amostras, consulte a fonte do [evento Service Bus](event-sources.md#service-bus).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

O Ônibus de Serviço emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.serviceBus.ActiveMessagesAvailableWithNoListeners | Levantados quando há mensagens ativas numa fila ou subscrição e nenhum recetor a ouvir. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Levantados quando há mensagens ativas numa Fila da Carta Morta e nenhum ouvinte ativo. |

## <a name="example-event"></a>Evento de exemplo

O exemplo que se segue mostra o esquema de mensagens ativas sem evento de ouvintes:

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

O esquema para um evento de fila de cartas mortas é semelhante:

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

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento de armazenamento blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| nomespaceName | string | O espaço de nome do Ônibus de serviço em que o recurso existe. |
| requestUri | string | O URI para a fila específica ou subscrição que emite o evento. |
| entidadeType | string | O tipo de entidade de ônibus de serviço que emite eventos (fila ou subscrição). |
| queueName | string | A fila com mensagens ativas se subscrever uma fila. Valor nulo se utilizar tópicos/subscrições. |
| tópicoNome | string | O tema a assinatura do Ônibus de serviço com mensagens ativas pertence. Valor nulo se utilizar uma fila. |
| subscriptionName | string | A subscrição do Ônibus de serviço com mensagens ativas. Valor nulo se utilizar uma fila. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para mais detalhes sobre a utilização da Rede de Eventos Azure com ônibus de serviço, consulte a visão geral da integração do [Bus de Serviço para a Rede de Eventos.](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)
* Experimente [receber eventos de ônibus de serviço com funções ou aplicações lógicas.](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json)
