---
title: Reagir a eventos do serviço Azure SignalR
description: Utilize o Azure Event Grid para subscrever a eventos do serviço Azure SignalR.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789192"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagir a eventos do serviço Azure SignalR

Os eventos de serviço SignalR do Azure permitem que os aplicativos reagir a ligações de cliente conectado ou desconectado usando as modernas arquiteturas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de consulta dispendiosa e ineficiente.  Em vez disso, os eventos são enviados por meio [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para os assinantes, tal como [as funções do Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio serviço de escuta de http personalizado e apenas pague apenas aquilo que utiliza.

Eventos de serviço SignalR do Azure com fiabilidade são enviados para o serviço de Event Grid que fornece serviços de entrega fiável às suas aplicações através de rich Repita políticas e entrega de mensagens não entregues. Para obter mais informações, consulte [entrega de mensagens do Event Grid e volte a tentar](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Modelo de grade do evento](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Estado sem servidor
Eventos de serviço SignalR do Azure só estão ativos quando ligações de cliente estão num estado sem servidor. Em termos gerais, se um cliente não encaminhar para um servidor de hub, ele vai para o estado sem servidor. Trabalho de modo clássico somente quando o hub, que ligar ligações de cliente, não tem um servidor de hub. No entanto, o modo sem servidor é recomendado para evitar a algum problema. Para obter mais detalhes sobre o modo de serviço, veja [como escolher o modo de serviço](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventos do serviço Azure SignalR disponíveis
Grelha de eventos usa [subscrições de eventos](../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Subscrições de eventos de serviço SignalR do Azure suportam dois tipos de eventos:  

|Nome do evento|Descrição|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Gerado quando uma ligação de cliente está ligada.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Gerado quando uma ligação de cliente está desconectada.|

## <a name="event-schema"></a>Esquema de eventos
Eventos do Azure do serviço SignalR contenham todas as informações que necessárias para responder a alterações nos seus dados. É possível identificar um serviço Azure SignalR eventos com a propriedade eventType começa com "Microsoft.SignalRService". Informações adicionais sobre a utilização das propriedades de eventos do Event Grid são documentadas em [esquema de eventos do Event Grid](../event-grid/event-schema.md).  

Eis um exemplo de um evento de ligados de ligação de cliente:
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

Para obter mais informações, consulte [esquema de eventos do serviço SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Event Grid e dê uma chance de eventos do serviço Azure SignalR:

> [!div class="nextstepaction"]
> [Tente uma integração do Event Grid de exemplo com o serviço Azure SignalR](./signalr-howto-event-grid-integration.md)
> [sobre o Event Grid](../event-grid/overview.md)
