---
title: Ônibus de serviço Azure - diferimento de mensagem
description: Este artigo explica como adiar a entrega de mensagens azure service bus. A mensagem permanece na fila ou subscrição, mas é reservada.
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
ms.openlocfilehash: 249cf7414143f59540d198bb460d8b215f6a7664
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756356"
---
# <a name="message-deferral"></a>Diferimento de mensagens

Quando um cliente de fila ou assinatura recebe uma mensagem que está disposta a processar, mas para o qual o processamento não é possível no momento devido a circunstâncias especiais dentro do aplicativo, ele tem a opção de "adiar" a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou subscrição, mas é reservada.

O adiamento é um recurso criado especificamente para cenários de processamento de fluxo de trabalho. As estruturas de fluxo de trabalho podem exigir que determinadas operações sejam processadas em uma ordem específica e talvez precisem adiar o processamento de algumas mensagens recebidas até que o trabalho anterior prescrito, informado por outras mensagens, tenha sido concluído.

Um exemplo ilustrativo simples é uma sequência de processamento de pedidos em que uma notificação de pagamento de um provedor de pagamento externo aparece em um sistema antes que a ordem de compra de correspondência seja propagada do front do armazenamento para o sistema de preenchimento. Nesse caso, o sistema de preenchimento pode adiar o processamento da notificação de pagamento até que haja uma ordem com a qual associá-la. Em cenários de reunião, em que as mensagens de origens diferentes impulsionam um fluxo de trabalho, a ordem de execução em tempo real pode realmente estar correta, mas as mensagens que refletem os resultados podem chegar fora de ordem.

Por fim, os auxílios de adiamento na reordenação de mensagens da ordem de chegada em uma ordem na qual elas podem ser processadas, deixando essas mensagens com segurança no repositório de mensagens para as quais o processamento precisa ser adiado.

## <a name="message-deferral-apis"></a>APIs de adiamento de mensagens

A API é [BrokeredMessage. Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente .NET Framework, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente .net Standard e [IMessageReceiver. Defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) ou [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) no cliente Java. 

As mensagens adiadas permanecem na fila principal, juntamente com todas as outras mensagens ativas (ao contrário de mensagens mortas que residem em uma subfila), mas não podem mais ser recebidas usando as funções Receive/ReceiveAsync regulares. As mensagens adiadas podem ser descobertas por meio da [procura](message-browsing.md) de mensagens se um aplicativo perder o controle delas.

Para recuperar uma mensagem adiada, seu proprietário é responsável por lembrar a [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , pois ela a adia. Qualquer destinatário que conheça o número de sequência de uma mensagem adiada pode receber a mensagem explicitamente mais tarde com `Receive(sequenceNumber)`. Para filas, você pode usar o [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), as assinaturas de tópico usam o [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient).

Se uma mensagem não puder ser processada porque um recurso específico para lidar com essa mensagem está temporariamente indisponível, mas o processamento de mensagens não deve ser suspenso de forma resumido, uma maneira de colocar essa mensagem no lado por alguns minutos é lembrar-se de **SequenceNumber** em uma [mensagem agendada](message-sequencing.md) a ser lançada em alguns minutos e recuperar novamente a mensagem adiada quando a mensagem agendada chegar. Se um manipulador de mensagens depender de um banco de dados para todas as operações e esse banco de dados estiver temporariamente indisponível, ele não deverá usar o adiamento, mas suspenderá o recebimento de mensagens completamente até que o banco de dados esteja disponível novamente.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
