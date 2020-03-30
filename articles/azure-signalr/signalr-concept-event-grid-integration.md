---
title: Reagir a eventos do Serviço De Sinalização Azure
description: Utilize a Grelha de Eventos Azure para subscrever os eventos do Serviço De Sinalização Azure. Outros serviços a jusante podem ser desencadeados por estes eventos.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158203"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagir aos eventos do Azure SignalR Service

Os eventos do Serviço De Sinalização Azure permitem que as aplicações reajam às ligações do cliente conectadas ou desligadas utilizando arquiteturas modernas sem servidor. Fá-lo sem a necessidade de código sumo ou serviços de sondagens dispendiosos e ineficientes.  Em vez disso, os eventos são empurrados através da Rede de [Eventos Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como [Funções Azure](https://azure.microsoft.com/services/functions/), [Aplicações Lógicas Azure,](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para o seu próprio ouvinte personalizado http, e você só paga pelo que você usa.

Os eventos do Serviço De Sinalização Azure são enviados de forma fiável para o serviço Da Rede de Eventos, que fornece serviços de entrega fiáveis às suas aplicações através de ricas políticas de retry e entrega de cartas mortas. Para saber mais, consulte a [entrega e a retentação da mensagem da Rede de Eventos.](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)

![Modelo de grelha de eventos](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Estado sem servidor
Os eventos do Serviço De Sinalização Azure só estão ativos quando as ligações com o cliente estão em estado de servidor. Em termos gerais, se um cliente não se encaminha para um servidor de hub, vai para o estado sem servidores. O modo clássico só funciona quando o hub, a que as ligações do cliente se ligam, não tem um servidor hub. No entanto, recomenda-se o modo sem servidores para evitar algum problema. Para saber mais detalhes sobre o modo de serviço, consulte [Como escolher o Modo de Assistência](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventos de serviço de sinalização Azure disponíveis
A grelha do evento utiliza subscrições de [eventos](../event-grid/concepts.md#event-subscriptions) para direcionar mensagens de eventos para assinantes. As subscrições de eventos do Serviço De Sinalização Azure suportam dois tipos de eventos:  

|Nome do Evento|Descrição|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Criado quando uma ligação com um cliente está ligada.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Levantada quando uma ligação ao cliente é desligada.|

## <a name="event-schema"></a>Esquema de eventos
Os eventos do Serviço De Sinalização Azure contêm todas as informações necessárias para responder às alterações dos seus dados. Pode identificar um evento do Serviço De Sinalização Azure com o eventoA propriedade Type começa com "Microsoft.SignalRService". Informações adicionais sobre a utilização das propriedades do evento Grid do evento estão documentadas no [event grid schema](../event-grid/event-schema.md).  

Aqui está um exemplo de um evento ligado à ligação ao cliente:
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

Para mais informações, consulte o esquema de eventos do [Serviço SignalR](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grelha de Eventos e dê aos eventos do Serviço De Sinalização Azure uma tentativa:

> [!div class="nextstepaction"]
> [Experimente uma amostra de integração da Grelha](./signalr-howto-event-grid-integration.md)
> de Eventos com o Serviço de Sinalização Azure sobre a[Grelha de Eventos](../event-grid/overview.md)
