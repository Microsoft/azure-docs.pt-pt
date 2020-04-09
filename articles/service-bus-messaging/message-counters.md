---
title: Ônibus de serviço Azure - contagem de mensagens
description: Recupere a contagem de mensagens realizadas em filas e subscrições utilizando o Azure Resource Manager e o Azure Service Bus NamespaceManager APIs.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891769"
---
# <a name="message-counters"></a>Contadores de mensagens

Pode recuperar a contagem de mensagens realizadas em filas e subscrições utilizando o Gestor de Recursos Azure e o Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs no .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o PowerShell, pode obter a contagem da seguinte forma:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes da contagem de mensagens

Saber a contagem de mensagens activaé útil para determinar se uma fila acumula um atraso que requer mais recursos para processar do que o que foi atualmente implementado. Os seguintes contra-detalhes estão disponíveis na classe [MessageCountDetails:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Mensagens na fila ou subscrição que estão no estado ativo e prontas para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Mensagens na fila da letra morta.
-   [Contagem de Mensagens Agendadas](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Mensagens no estado programado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Mensagens que falharam a transferência para outra fila ou tópico e foram transferidas para a fila de transferências de cartas mortas.
-   [TransferênciaMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Mensagens pendentes de transferência para outra fila ou tópico.

Se uma aplicação quiser escalar os recursos com base no comprimento da fila, deve fazê-lo com um ritmo medido. A aquisição dos contadores de mensagens é uma operação dispendiosa dentro do corretor de mensagens, e executá-la frequentemente direta e negativamente impacta o desempenho da entidade.

> [!NOTE]
> As mensagens que são enviadas para um tópico de Ônibus de serviço são reencaminhadas para subscrições para esse tópico. Assim, a mensagem ativa conta com o tema em si é 0, uma vez que essas mensagens foram reencaminhadas com sucesso para a subscrição. Obtenha a contagem de mensagens na subscrição e verifique se é maior que 0. Apesar de ver mensagens na subscrição, elas são armazenadas num armazém propriedade do tema. 

Se olharmos para as subscrições, então elas teriam uma contagem de mensagens não nula (que somam até 323MB de espaço para toda esta entidade).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
