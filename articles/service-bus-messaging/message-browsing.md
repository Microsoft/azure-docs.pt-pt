---
title: Azure Service Bus - navegação de mensagens
description: Navegue e espreite as mensagens Service Bus permite que um cliente da Azure Service Bus enumere todas as mensagens numa fila ou subscrição.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553646"
---
# <a name="message-browsing"></a>Navegação de mensagens

A navegação de mensagens, ou olhando, permite que um cliente do Service Bus enumerou todas as mensagens numa fila ou numa subscrição, para fins de diagnóstico e depuragem.

A operação de espreitar numa fila devolve todas as mensagens na fila, não apenas as disponíveis para aquisição imediata com `Receive()` ou o `OnMessage()` loop. A `State` propriedade de cada mensagem indica-lhe se a mensagem está ativa (disponível para ser recebida), [adiada](message-deferral.md)ou [agendada.](message-sequencing.md) A operação de espreitar uma subscrição retorna todas as mensagens exceto mensagens programadas no registo de mensagens de subscrição. 

As mensagens consumidas e expiradas são limpas por uma "recolha de lixo" assíncronea. Este passo pode não ocorrer necessariamente imediatamente após a expiração das mensagens. É por isso `Peek` que, pode devolver mensagens que já expiraram. Estas mensagens serão removidas ou enviadas com letras mortas quando uma operação de receção for invocada na fila ou subscrição da próxima vez. Tenha este comportamento em mente ao tentar recuperar mensagens diferidas da fila. Uma mensagem expirada já não é elegível para recuperação regular por qualquer outro meio, mesmo quando é devolvida por Peek. A devolução destas mensagens é por design, uma vez que Peek é uma ferramenta de diagnóstico que reflete o estado atual do registo.

Peek também devolve mensagens que estavam bloqueadas e estão atualmente a ser processadas por outros recetores. No entanto, como Peek devolve uma imagem desligada, o estado de bloqueio de uma mensagem não pode ser observado em mensagens espreitadas. As propriedades [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) lançam uma [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando a aplicação tenta lê-las.

## <a name="peek-apis"></a>Espreite as APIs

Os métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existem nas bibliotecas clientes .NET e Java e em objetos recetores: **MessageReceiver**, **MessageSession**. Peek trabalha em filas, subscrições e respetivas filas de cartas mortas.

Quando chamado repetidamente, **Peek** enumera todas as mensagens na fila ou registo de subscrição, por ordem, do número de sequência mais baixo disponível para o mais alto. É a ordem em que as mensagens foram encosadas, não a ordem pela qual as mensagens podem eventualmente ser recuperadas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera várias mensagens e devolve-as como uma enumeração. Se não houver mensagens disponíveis, o objeto de enumeração está vazio, não nulo.

Também pode utilizar uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) para começar e, em seguida, chamar a sobrecarga do método sem parâmetros para enumerar ainda mais. **O PeekBatch** funciona de forma equivalente, mas recupera um conjunto de mensagens de uma só vez.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
