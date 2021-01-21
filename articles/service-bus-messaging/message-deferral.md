---
title: Azure Service Bus - adiamento de mensagem
description: Este artigo explica como adiar a entrega de mensagens Azure Service Bus. A mensagem permanece na fila ou subscrição, mas é reservada.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: e3a940f8aa9e72d9b09e9c0a3305521c6f17dfb0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622050"
---
# <a name="message-deferral"></a>Diferimento de mensagens

Quando um cliente de fila ou subscrição recebe uma mensagem que está disposto a processar, mas para a qual o processamento não é atualmente possível devido a circunstâncias especiais dentro da aplicação, tem a opção de "adiar" a recuperação da mensagem para um ponto posterior. A mensagem permanece na fila ou subscrição, mas é reservada.

O adiamento é uma funcionalidade criada especificamente para cenários de processamento de fluxo de trabalho. Os quadros de fluxo de trabalho podem exigir que determinadas operações sejam processadas numa determinada ordem, e podem ter de adiar o processamento de algumas mensagens recebidas até que os trabalhos prévios prescritos que sejam informados por outras mensagens tenham sido concluídos.

Um simples exemplo ilustrativo é uma sequência de processamento de encomendas em que uma notificação de pagamento de um prestador de pagamento externo aparece num sistema antes de a ordem de compra correspondente ter sido propagada da frente da loja para o sistema de realização. Nesse caso, o sistema de cumprimento pode adiar o processamento da notificação de pagamento até que exista uma ordem para a associar. Em cenários de encontro, em que mensagens de diferentes fontes impulsionam um fluxo de trabalho para a frente, a ordem de execução em tempo real pode mesmo estar correta, mas as mensagens que refletem os resultados podem chegar fora de ordem.

Em última análise, os auxiliares de diferimento na reordenação das mensagens da ordem de chegada para uma ordem em que podem ser processadas, deixando essas mensagens em segurança na loja de mensagens para as quais o processamento precisa de ser adiado.

> [!NOTE]
> As mensagens diferidas não serão automaticamente transferidas para a fila das letras mortas após a [sua expiração](./service-bus-dead-letter-queues.md#exceeding-timetolive). Este comportamento é por desígnio.

## <a name="message-deferral-apis"></a>APIs de diferimento de mensagem

A API é [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) ou [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) no cliente .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) no cliente .NET Standard, e [IMessageReceiver.defereiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer) no cliente Java. [](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync) 

As mensagens diferidas permanecem na fila principal juntamente com todas as outras mensagens ativas (ao contrário de mensagens de letra morta que vivem num subqueue), mas já não podem ser recebidas usando as funções regulares de Receber/Receber. As mensagens diferidas podem ser descobertas através da [navegação por mensagens](message-browsing.md) se uma aplicação perder o controlo das mesmos.

Para recuperar uma mensagem diferida, o seu proprietário é responsável por lembrar o [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) à medida que a adia. Qualquer recetor que conheça o número de sequência de uma mensagem diferida pode receber a mensagem explicitamente com `Receive(sequenceNumber)` .

Se uma mensagem não puder ser processada porque um determinado recurso para o manuseamento dessa mensagem está temporariamente indisponível, mas o processamento de mensagens não deve ser sumariamente suspenso, uma forma de colocar essa mensagem no lado durante alguns minutos é lembrar o **SequenceNumber** numa [mensagem programada](message-sequencing.md) a ser publicada em poucos minutos e recuperar a mensagem diferida quando a mensagem programada chegar. Se um manipulador de mensagens depender de uma base de dados para todas as operações e essa base de dados estiver temporariamente indisponível, não deve utilizar o adiamento, mas sim suspender a receção de mensagens completamente até que a base de dados esteja novamente disponível.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
