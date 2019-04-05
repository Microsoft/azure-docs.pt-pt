---
title: Contagem de mensagens do Service Bus do Azure | Documentos da Microsoft
description: Obter a contagem de mensagens do Service bus do Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050752"
---
# <a name="message-counters"></a>Contadores de mensagens

Pode obter a contagem de mensagens contidas nas filas e subscrições através da utilização do Azure Resource Manager e o Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) APIs no SDK do .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o PowerShell, pode obter a contagem da seguinte forma:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes da contagem de mensagem

É útil para determinar se uma fila cria uma lista de pendências que requer mais recursos para processar do que o que atualmente tiver sido implementado saber a contagem de mensagens do Active Directory. Os seguintes detalhes de contador estão disponíveis no [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) classe:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Mensagens na fila ou subscrição ativo estaduais e pronto para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Mensagens na fila de mensagens não entregues.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Mensagens no Estado agendado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Mensagens de falha de transferência para outra fila ou tópico e foram movidas para a fila de mensagens não entregues de transferência.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Mensagens pendentes transferência em outra fila ou tópico.

Se um aplicativo quiser Dimensionar recursos com base no comprimento da fila, ele deve fazer isso com uma medida ritmo. A aquisição dos contadores de mensagem é uma operação dispendiosa dentro do Mediador de mensagens e executá-la com frequência diretamente e negativa afeta o desempenho de entidade.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
