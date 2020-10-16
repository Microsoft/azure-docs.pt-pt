---
title: Azure SignalR como fonte de grade de eventos
description: Descreve as propriedades que são fornecidas para eventos Azure SignalR com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106656"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de eventos Azure Event Grid para o Serviço SignalR

Este artigo fornece as propriedades e esquema para eventos do Serviço SignalR.Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). Também lhe dá uma lista de partidas rápidas e tutoriais para usar o Azure SignalR como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

O Serviço SignalR emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Levantado quando uma ligação com o cliente está ligada. |
| Microsoft.signalrService.ClientConnectionDis ligados | Levantado quando uma ligação do cliente foi desligada. |

### <a name="example-event"></a>Exemplo evento

O exemplo a seguir mostra o esquema de um evento ligado à ligação do cliente: 

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

O esquema de um evento desligado de ligação ao cliente é semelhante: 

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados do evento do Serviço SignalR. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| carimbo de data/hora | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| nome hub | string | O centro a que pertence a ligação com o cliente. |
| connectionId | string | O identificador único para a ligação ao cliente. |
| userId | string | O identificador de utilizador definido na reclamação. |
| erroMessage | string | O erro que provoca a ligação desligada. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título | Descrição |
|---------|---------|
| [Reagir aos eventos do Serviço Azure SignalR utilizando a Grade de Eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do Serviço Azure SignalR com Grelha de Eventos. |
| [Como enviar eventos do Serviço Azure SignalR para a Grelha de Eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do Serviço Azure SignalR para uma aplicação através da Grade de Eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
