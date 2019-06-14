---
title: Navegação de mensagens do Service Bus do Azure | Documentos da Microsoft
description: Procurar e espie mensagens do Service Bus
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
ms.openlocfilehash: 425cf262b80e83a4d06074a567a2921eee12f9c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402756"
---
# <a name="message-browsing"></a>Navegação de mensagens

A procura de mensagem ou observação, permite que um cliente de Service Bus enumerar todas as mensagens que residem numa fila ou subscrição, normalmente, para fins de diagnósticos e depuração.

As operações de pré-visualização retornam todas as mensagens que existem no registo de mensagem fila ou subscrição, não apenas os disponíveis para aquisição de imediato com `Receive()` ou o `OnMessage()` loop. O `State` propriedade de cada mensagem informa se a mensagem está ativa (disponível para ser recebida), [diferida](message-deferral.md), ou [agendada](message-sequencing.md).

Mensagens consumidas e expiradas são limpos pelo assíncrona "coleta de lixo", executar e não necessariamente exatamente quando as mensagens expiram e, portanto, `Peek` , de fato, podem devolver mensagens que já têm a expirou e serão removidas ou quando lettered em papel já era uma operação de recebimento em seguida é invocada na fila ou subscrição.

Isso é especialmente importante a ter em conta quando tenta recuperar diferidas mensagens da fila. Uma mensagem para o qual o [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) instantâneas passou já não é elegível para obtenção regular de outros meios, mesmo quando estão a ser devolvido por Peek. Essas mensagens a devolver é proposital, uma vez que a pré-visualização é uma ferramenta de diagnóstico que reflete o estado atual do registo.

Observar também devolve mensagens que foram bloqueadas e estão atualmente a ser processadas por outros recetores, mas ainda não foram concluídas. No entanto, como pré-visualização retorna um instantâneo de desligado, o estado de bloqueio de uma mensagem não pode ser observado nas mensagens de pré-visualizar e o [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) propriedades throw um [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando a aplicação tenta lê-los.

## <a name="peek-apis"></a>APIs de pré-visualização

O [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) métodos existem nas bibliotecas de cliente de todos os .NET e Java e em todos os objetos de destinatário: **MessageReceiver**, **MessageSession**, **QueueClient**, e **SubscriptionClient**. Pré-visualização funciona em todas as filas e subscrições e suas respectivas filas de mensagens não entregues.

Quando chamado repetidamente, o método de pré-visualização enumera todas as mensagens que existem no registo de fila ou subscrição, na ordem de número de sequência, do número mais baixo sequência disponíveis para o maior. Esta é a ordem na qual as mensagens foram colocados em fila e não é a ordem na qual mensagens, eventualmente, poderão ser recuperadas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) obtém várias mensagens e devolve-os como uma enumeração. Se nenhuma mensagem estiver disponível, o objeto de enumeração é não nulo, estar vazio.

Também pode propagar uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) na qual deve começar e, em seguida, chame a sobrecarga do método sem parâmetros para enumerar ainda mais. **PeekBatch** funciona de forma equivalente, mas obtém um conjunto de mensagens ao mesmo tempo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
