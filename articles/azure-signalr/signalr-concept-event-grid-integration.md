---
title: Reagir aos eventos do Serviço Azure SignalR
description: Utilize a Grelha de Eventos Azure para subscrever eventos do Serviço Azure SignalR. Outros serviços a jusante podem ser desencadeados por estes eventos.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151105"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagir aos eventos do Azure SignalR Service

Os eventos do Serviço Azure SignalR permitem que as aplicações reajam às ligações do cliente conectadas ou desligadas utilizando arquiteturas modernas sem servidor. Fá-lo sem a necessidade de um código complicado ou de serviços de sondagens dispendiosos e ineficientes.  Em vez disso, os eventos são empurrados através [da Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio ouvinte personalizado. Com o Azure SignalR, só se paga pelo que consome.

Os eventos do Serviço Azure SignalR são enviados de forma fiável para o serviço de Grade de Eventos que fornece serviços de entrega fiáveis às suas aplicações através de políticas de relagem ricas e entrega de cartas mortas. Para saber mais, consulte a [entrega de mensagens de Event Grid e retentou.](../event-grid/delivery-and-retry.md)

![Modelo de grelha de evento](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Estado sem servidor
Os eventos do Serviço Azure SignalR só estão ativos quando as ligações do cliente estão num estado sem servidor. Se um cliente não se encaminhar para um servidor de hub, ele vai para o estado sem servidor. O modo clássico só funciona quando o hub a que as ligações do cliente se ligam não tem um servidor hub. O modo serverless é recomendado como uma boa prática. Para obter mais detalhes sobre o modo de serviço, consulte [Como escolher o Modo de Serviço](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventos disponíveis do Serviço Azure SignalR
A grelha de eventos utiliza [subscrições de eventos](../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de evento para assinantes. As subscrições do evento Azure SignalR Service suportam dois tipos de eventos:  

|Nome do Evento|Description|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Levantado quando uma ligação com o cliente está ligada.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Levantado quando a ligação do cliente é desligada.|

## <a name="event-schema"></a>Esquema de eventos
Os eventos do Serviço Azure SignalR contêm toda a informação necessária para responder às alterações dos seus dados. Pode identificar um evento do Serviço Azure SignalR com a propriedade eventType começa com "Microsoft.SignalRService". Informações adicionais sobre a utilização das propriedades do evento Event Grid estão documentadas no [esquema de eventos da Grade de Eventos.](../event-grid/event-schema.md)  

Aqui está um exemplo de um evento ligado à ligação do cliente:
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

Para mais informações, consulte [o esquema de eventos do Serviço SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê um teste aos eventos do Serviço Azure SignalR:

> [!div class="nextstepaction"]
> [Experimente uma integração de grade de eventos com o Serviço](./signalr-howto-event-grid-integration.md) 
>  Azure SignalR [Sobre a Grelha de Eventos](../event-grid/overview.md)