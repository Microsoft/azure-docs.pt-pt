---
title: Azure Service Bus - navegação de mensagens
description: Navegue e espreite as mensagens Service Bus permite que um cliente da Azure Service Bus enumerou todas as mensagens que residem numa fila ou subscrição.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6e50fc737f6c81c07854ff07d8cc64061306749b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827450"
---
# <a name="message-browsing"></a>Navegação de mensagens

A navegação de mensagens, ou olhando, permite que um cliente do Service Bus enumerou todas as mensagens que residem numa fila ou subscrição, tipicamente para fins de diagnóstico e depuragem.

As operações de espreitar devolvem todas as mensagens que existem no registo de mensagens de fila ou subscrição, não apenas as disponíveis para aquisição imediata com `Receive()` ou o `OnMessage()` loop. A `State` propriedade de cada mensagem indica-lhe se a mensagem está ativa (disponível para ser recebida), [adiada](message-deferral.md)ou [agendada.](message-sequencing.md)

As mensagens consumidas e expiradas são limpas por uma "recolha de lixo" assíncrona e não necessariamente quando as mensagens expiram, podendo, `Peek` portanto, devolver mensagens que já expiraram e serão removidas ou enviadas quando uma operação de receção é invocada na fila ou subscrição.

Isto é especialmente importante para ter em mente quando se tenta recuperar mensagens diferidas da fila. Uma mensagem para a qual o instantâneo [ExpirasAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) já não é elegível para recuperação regular por qualquer outro meio, mesmo quando é devolvido por Peek. A devolução destas mensagens é deliberada, uma vez que Peek é uma ferramenta de diagnóstico que reflete o estado atual do registo.

Peek também devolve mensagens que estavam bloqueadas e estão a ser processadas por outros recetores, mas ainda não foram concluídas. No entanto, como o Peek devolve uma imagem desligada, o estado de bloqueio de uma mensagem não pode ser observado em mensagens espreitadas, e as propriedades [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) lançam uma [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando a aplicação tenta lê-las.

## <a name="peek-apis"></a>Espreite as APIs

Os métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existem em todas as bibliotecas clientes .NET e Java e em todos os objetos recetores: **MessageReceiver**, **MessageSession**. Peek trabalha em todas as filas e subscrições e respetivas filas de letras mortas.

Quando chamado repetidamente, o método Peek enumera todas as mensagens que existem na fila ou no registo de subscrição, na ordem de número de sequência, do número de sequência mais baixo disponível para o mais alto. Esta é a ordem em que as mensagens foram encosadas e não é a ordem em que as mensagens podem eventualmente ser recuperadas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera várias mensagens e devolve-as como uma enumeração. Se não houver mensagens disponíveis, o objeto de enumeração está vazio, não nulo.

Também pode utilizar uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) para começar e, em seguida, chamar a sobrecarga do método sem parâmetros para enumerar ainda mais. **O PeekBatch** funciona de forma equivalente, mas recupera um conjunto de mensagens de uma só vez.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
