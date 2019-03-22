---
title: Diferimento de mensagens do Service Bus do Azure | Documentos da Microsoft
description: Diferir a entrega de mensagens do Service Bus
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
ms.openlocfilehash: 11ea10f1deba5a21b98dea875a1b7dc94998aa00
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225169"
---
# <a name="message-deferral"></a>Diferimento de mensagens

Quando um cliente de fila ou subscrição recebe uma mensagem que está disposto a processar, mas para que o processamento não é atualmente possível devido a circunstâncias especiais dentro da aplicação, tem a opção de obtenção da mensagem para depois de "diferir". A mensagem permanece na fila ou subscrição, mas é reservada.

Diferimento é uma funcionalidade criada especificamente para cenários de processamento de fluxo de trabalho. Estruturas de fluxo de trabalho podem exigir determinadas operações a serem processados numa ordem específica e talvez tenha que adiar o processamento de algumas mensagens recebidas até estar concluído prescrito trabalho anterior que é informado por outras mensagens.

Um exemplo ilustrativa simples é uma ordem de processamento de sequência em que uma notificação de pagamento de um fornecedor de pagamento externo aparece num sistema antes da ordem de compra correspondente tiver sido propagada da loja para o sistema de preenchimento. Nesse caso, o sistema de preenchimento pode diferir a processar a notificação de pagamento, até que haja um pedido com o qual pretende associá-la. Em cenários de rendezvous, onde as mensagens de diferentes origens unidade um fluxo de trabalho para a frente, a ordem de execução em tempo real pode realmente ser correta, mas as mensagens que reflete os resultados podem chegar fora de ordem.

Por fim, diferimento auxilia a reordenação de mensagens da ordem de chegada numa ordem na qual eles podem ser processados, mantendo essas mensagens com segurança no arquivo de mensagem para as necessidades de processamento ser adiada.

## <a name="message-deferral-apis"></a>APIs de diferimento de mensagens

A API é [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente do .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente .NET Standard, e **mesageReceiver.defer** ou **messageReceiver.deferSync** no cliente de Java. 

Mensagens diferidas permanecem na fila de principal, juntamente com todas as outras mensagens Active Directory (ao contrário de mensagens não entregues que residem numa subfila), mas já não pode ser recebidos usando as funções de recebimento/ReceiveAsync regulares. Mensagens diferidas possam ser detetadas através de [navegação de mensagens](message-browsing.md) se um aplicativo perder o controle deles.

Para obter uma mensagem diferida, o respetivo proprietário é responsável por tendo em conta a [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) como ele difere-lo. Qualquer recetor que sabe o número de sequência de uma mensagem diferida mais tarde pode receber a mensagem explicitamente com `Receive(sequenceNumber)`. Para as filas, pode utilizar o [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), subscrições de tópicos de utilizar o [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient).

Se uma mensagem não é possível processar porque um recurso específico para processar essa mensagem está temporariamente indisponível, mas o processamento de mensagem não deve ser suspenso summarily, uma forma de colocar essa mensagem no lado durante alguns minutos, é se lembrar do  **SequenceNumber** num [mensagem agendada](message-sequencing.md) publicadas em alguns minutos e, novamente obter a mensagem diferida quando chega a mensagem agendada. Se um manipulador de mensagens depende de uma base de dados para todas as operações e essa base de dados está temporariamente indisponível, ele deve não utilizar diferimento, mas em vez disso, suspender o recebimento de mensagens totalmente até que a base de dados estiver novamente disponível.


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
