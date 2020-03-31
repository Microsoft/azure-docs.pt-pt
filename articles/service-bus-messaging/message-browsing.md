---
title: Ônibus de serviço Azure - navegação de mensagens
description: Navegue e espreite as mensagens service Bus permite a um cliente do Azure Service Bus enumerar todas as mensagens que residem numa fila ou subscrição.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539370"
---
# <a name="message-browsing"></a>Navegação de mensagens

A navegação por mensagens, ou espreitar, permite que um cliente do Service Bus enumere todas as mensagens que residem numa fila ou subscrição, normalmente para fins de diagnóstico e depuração.

As operações de peek devolvem todas as mensagens existentes no `Receive()` registo `OnMessage()` de mensagens de fila ou subscrição, não apenas as disponíveis para aquisição imediata com ou o loop. A `State` propriedade de cada mensagem diz-lhe se a mensagem está ativa (disponível para ser recebida), [adiada,](message-deferral.md)ou [programada.](message-sequencing.md)

As mensagens consumidas e expiradas são limpas por uma "recolha de lixo" assíncrona e não necessariamente quando as mensagens expiram, pelo que `Peek` podem, de facto, devolver mensagens que já tenham expirado e serão removidas ou mortas quando uma operação de receção for invocada na fila ou subscrição.

Isto é especialmente importante ter em mente quando se tenta recuperar mensagens diferidas da fila. Uma mensagem para a qual o instante [ExpirasAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) passou já não é elegível para recuperação regular por qualquer outro meio, mesmo quando está a ser devolvido por Peek. Devolver estas mensagens é deliberado, uma vez que peek é uma ferramenta de diagnóstico que reflete o estado atual do registo.

Peek também devolve mensagens que foram bloqueadas e estão atualmente a ser processadas por outros recetores, mas ainda não foram concluídas. No entanto, como peek devolve uma imagem desconectada, o estado de bloqueio de uma mensagem não pode ser observado em mensagens espreitadas, e as propriedades [LockUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) lançam uma [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando a aplicação tenta lê-las.

## <a name="peek-apis"></a>Peek APIs

Os métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existem em todas as bibliotecas de clientes .NET e Java e em todos os objetos recetores: **MessageReceiver,** **MessageSession**. Peek trabalha em todas as filas e subscrições e suas respetivas filas de cartas mortas.

Quando chamado repetidamente, o método Peek enumera todas as mensagens existentes na fila ou registo de subscrição, em ordem de número de sequência, do menor número de sequência disponível para o mais alto. Esta é a ordem em que as mensagens foram enquecidas e não é a ordem em que as mensagens podem eventualmente ser recuperadas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera várias mensagens e devolve-as como uma enumeração. Se não houver mensagens disponíveis, o objeto de enumeração está vazio, não nulo.

Também pode semear uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) para iniciar e, em seguida, chamar a sobrecarga do método parametrômetro para enumerar mais. **PeekBatch** funciona de forma equivalente, mas recupera um conjunto de mensagens de uma só vez.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
