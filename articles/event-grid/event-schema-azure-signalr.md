---
title: Sinal de Azure como fonte da Grelha de Eventos
description: Descreve as propriedades que estão fornecidas para eventos Azure SignalR com Grelha de Eventos Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: babanisa
ms.openlocfilehash: e4ebae9597d750cea6f292655e9f03dd65ccc3f5
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133725"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de evento sinuoso do evento Azure Event Grid para o Serviço de Sinalizador

Este artigo fornece as propriedades e esquemas para eventos do SignalR Service.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md). Também lhe dá uma lista de arranques rápidos e tutoriais para usar o Azure SignalR como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

O Serviço SignalR emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.signalrService.ClientConnectionConnected | Criado quando uma ligação com um cliente ligou. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Levantada quando uma ligação de cliente se desligou. |

### <a name="example-event"></a>Evento de exemplo

O exemplo que se segue mostra o esquema de um evento ligado à ligação ao cliente: 

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

O esquema para um evento desligado de ligação ao cliente é semelhante: 

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
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento do Serviço SignalR. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| carimbo de data/hora | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| hubName | string | O centro a que a ligação com o cliente pertence. |
| conexãoId | string | O identificador único para a ligação do cliente. |
| userId | string | O identificador de utilizador definido na reivindicação. |
| errorMessage | string | O erro que causa a ligação desligada. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título | Descrição |
|---------|---------|
| [Reaja aos eventos do Serviço De Sinalização Azure utilizando a Grelha de Eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do Serviço de Sinalização Azure com grelha de eventos. |
| [Como enviar eventos do Serviço De Sinalização Azure para a Grelha de Eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do Serviço De Sinalização Azure para uma aplicação através da Rede de Eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
