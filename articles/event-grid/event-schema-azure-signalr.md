---
title: Esquema de eventos do Azure Event Grid Azure SignalR
description: Descreve as propriedades que são fornecidas para eventos do Azure SignalR com o Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789075"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de eventos do Azure Event Grid para o serviço SignalR

Este artigo fornece as propriedades e o esquema para eventos de serviço SignalR. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Tipos de eventos disponíveis

Serviço SignalR emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Desencadeado quando estiver ligado uma ligação de cliente. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Gerado quando uma ligação de cliente desconectada. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema de um cliente de eventos ligados de ligação: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento desconectado da ligação de cliente é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | Cadeia de caracteres | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| subject | Cadeia de caracteres | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | cadeia | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | Cadeia de caracteres | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| id | Cadeia de caracteres | Identificador exclusivo para o evento. |
| data | object | Dados de eventos do serviço de SignalR. |
| dataVersion | Cadeia de caracteres | A versão de esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | cadeia | A versão de esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| timestamp | cadeia | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| hubName | Cadeia de caracteres | O hub que pertence a ligação de cliente. |
| connectionId | Cadeia de caracteres | O identificador exclusivo para a ligação de cliente. |
| userId | Cadeia de caracteres | O identificador de utilizador definido na afirmação. |
| errorMessage | cadeia | O erro que faz com que a ligação desligado. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
