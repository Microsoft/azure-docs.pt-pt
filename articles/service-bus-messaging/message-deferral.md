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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538401"
---
# <a name="message-deferral"></a>Diferimento de mensagens

Quando um cliente de fila ou subscrição recebe uma mensagem que está disposto a processar, mas para a qual o processamento não é atualmente possível devido a circunstâncias especiais dentro da aplicação, tem a opção de "adiar" a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou subscrição, mas é reservada.

O diferimento é uma funcionalidade especificamente criada para cenários de processamento de fluxos de trabalho. Os quadros de fluxo de trabalho podem exigir que determinadas operações sejam processadas numa determinada ordem, podendo ter de adiar o processamento de algumas mensagens recebidas até que o trabalho prévio prescrito que seja informado por outras mensagens tenha sido concluído.

Um exemplo ilustrativo simples é uma sequência de processamento de encomendas em que uma notificação de pagamento de um prestador de pagamento externo aparece num sistema antes de a ordem de compra correspondente ter sido propagada da frente da loja para o sistema de realização. Nesse caso, o sistema de cumprimento poderá adiar o processamento da notificação de pagamento até que haja uma ordem para a associar. Em cenários de encontro, onde mensagens de diferentes fontes impulsionam um fluxo de trabalho para a frente, a ordem de execução em tempo real pode estar realmente correta, mas as mensagens que refletem os resultados podem chegar fora de ordem.

Em última análise, o adiamento ajuda a reordenar mensagens da ordem de chegada numa ordem em que podem ser processadas, deixando essas mensagens em segurança no depósito de mensagens para a qual o processamento precisa de ser adiado.

## <a name="message-deferral-apis"></a>APIs diferimento de mensagem

O API é [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente .NET Standard, e [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) ou [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) no cliente Java. 

As mensagens diferidas permanecem na fila principal juntamente com todas as outras mensagens ativas (ao contrário das mensagens de letra morta que vivem numa subfila), mas já não podem ser recebidas utilizando as funções regulares de Receção/Receção. As mensagens diferidas podem ser descobertas através da [navegação por mensagem](message-browsing.md) se uma aplicação perder o rasto das mesmas.

Para recuperar uma mensagem diferida, o seu proprietário é responsável por lembrar o [Número de Sequências](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) à medida que a adia. Qualquer recetor que conheça o número de sequência de uma mensagem diferida pode mais tarde receber a mensagem explicitamente com `Receive(sequenceNumber)`.

Se uma mensagem não puder ser processada porque um recurso específico para manusear essa mensagem está temporariamente indisponível, mas o processamento de mensagens não deve ser sumariamente suspenso, uma forma de colocar essa mensagem de lado por alguns minutos é lembrar o Número de **Sequências** numa [mensagem programada](message-sequencing.md) a ser publicada em poucos minutos, e recuperar a mensagem adiada quando a mensagem programada chegar. Se um manipulador de mensagens depender de uma base de dados para todas as operações e essa base de dados estiver temporariamente indisponível, não deve utilizar o adiamento, mas sim suspender a receção de mensagens até que a base de dados esteja novamente disponível.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
