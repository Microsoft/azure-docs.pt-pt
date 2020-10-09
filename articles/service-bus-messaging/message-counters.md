---
title: Azure Service Bus - contagem de mensagens
description: Recupere a contagem de mensagens realizadas em filas e subscrições utilizando o Azure Resource Manager e o Azure Service Bus NamespaceManager APIs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d0e1a7a5c6eb0b281b4e6ac08135f41f28ecbec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341267"
---
# <a name="message-counters"></a>Contadores de mensagens

Pode recuperar a contagem de mensagens mantidas em filas e subscrições utilizando o Azure Resource Manager e o Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs no .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o PowerShell, pode obter a contagem da seguinte forma:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes da contagem de mensagens

Conhecer a contagem de mensagens ativas é útil para determinar se uma fila constrói um atraso que requer mais recursos para processar do que o que foi atualmente implementado. Os seguintes dados do contador estão disponíveis na classe [MessageCountDetails:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Mensagens na fila ou subscrição que estão no estado ativo e prontas para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Mensagens na fila da letra morta.
-   [AgendadoMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Mensagens no estado programado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Mensagens que falharam na transferência para outra fila ou tópico e foram transferidas para a fila de transferências de cartas mortas.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Mensagens pendentes de transferência para outra fila ou tópico.

Se uma aplicação quiser escalar os recursos com base no comprimento da fila, deve fazê-lo com um ritmo medido. A aquisição dos contadores de mensagens é uma operação dispendiosa dentro do corretor de mensagens, e executá-la frequentemente direta e negativamente impacta o desempenho da entidade.

> [!NOTE]
> As mensagens enviadas para um tópico de Service Bus são reencaminhadas para subscrições para esse tópico. Assim, a contagem de mensagens ativas sobre o tema em si é 0, uma vez que essas mensagens foram reencaminhadas com sucesso para a subscrição. Obtenha a contagem de mensagens na subscrição e verifique se é maior que 0. Apesar de ver mensagens na subscrição, elas são armazenadas num armazenamento propriedade do tópico. 

Se olharmos para as subscrições, então terão uma contagem de mensagens não-zero (que somam 323MB de espaço para toda esta entidade).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
