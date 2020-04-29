---
title: Visão geral do processamento de transações no Ônibus de Serviço Azure
description: Este artigo dá-lhe uma visão geral do processamento de transações e do envio através de recurso no Azure Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79260907"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Visão geral do processamento de transações de ônibus de serviço

Este artigo discute as capacidades de transação do Microsoft Azure Service Bus. Grande parte da discussão é ilustrada pelas [Transações AMQP com amostra de ônibus de serviço.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) Este artigo limita-se a uma visão geral do processamento de transações e do *envio via* funcionalidade no Service Bus, enquanto a amostra de Transações Atómicas é mais ampla e complexa no âmbito.

## <a name="transactions-in-service-bus"></a>Transações em Ônibus de Serviço

Uma *transação agrupa* duas ou mais operações em conjunto num âmbito de *execução.* Por natureza, essa transação deve assegurar que todas as operações pertencentes a um determinado grupo de operações tenham êxito ou falhem conjuntamente. A este respeito, as transações funcionam como uma unidade, que é muitas vezes referida como *atómico.*

O Service Bus é um corretor de mensagens transacionais e garante a integridade transacional de todas as operações internas contra as suas lojas de mensagens. Todas as transferências de mensagens dentro do Service Bus, tais como a deslocação de mensagens para uma [fila de cartas mortas](service-bus-dead-letter-queues.md) ou o [reencaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre entidades, são transacionais. Como tal, se a Service Bus aceitar uma mensagem, já foi armazenada e rotulada com um número de sequência. A partir daí, quaisquer transferências de mensagens dentro do Service Bus são operações coordenadas entre entidades, e não levarão a perdas (fonte sucedânea e falha de alvo) ou à duplicação (falha de origem e alvo sucede) da mensagem.

O Service Bus suporta operações de agrupamento em relação a uma entidade de mensagens única (fila, tópico, subscrição) no âmbito de uma transação. Por exemplo, pode enviar várias mensagens para uma fila a partir de um âmbito de transação, e as mensagens só serão comprometidas com o registo da fila quando a transação for concluída com sucesso.

## <a name="operations-within-a-transaction-scope"></a>Operações dentro de um âmbito de transação

As operações que podem ser realizadas dentro de um âmbito de transação são as seguintes:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Enviar, EnviarAsync, Enviar, EnviarAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

As operações de receção não estão incluídas, pois presume-se que a aplicação adquire mensagens utilizando o modo [ReceiveMode.PeekLock,](/dotnet/api/microsoft.azure.servicebus.receivemode) dentro de algum loop de receção ou com um backback [OnMessage,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e só então abre uma margem de transação para o processamento da mensagem.

A disposição da mensagem (completa, abandono, letra morta, adiamento) ocorre então no âmbito e dependente do resultado global da transação.

## <a name="transfers-and-send-via"></a>Transfers e "enviar via"

Para permitir a transferência de dados transacionais de uma fila para um processador e, em seguida, para outra fila, o Service Bus suporta *transferências*. Numa operação de transferência, um remetente envia primeiro uma mensagem para uma fila de *transferências*, e a fila de transferências move imediatamente a mensagem para a fila de destino pretendida usando a mesma implementação robusta de transferência em que a capacidade de auto-encaminhamento depende. A mensagem nunca está comprometida com o registo da fila de transferências de uma forma que se torne visível para os consumidores da fila de transferências.

O poder desta capacidade transacional torna-se evidente quando a própria fila de transferências é a fonte das mensagens de entrada do remetente. Por outras palavras, o Service Bus pode transferir a mensagem para a fila de destino "via" a fila de transferências, enquanto executa uma operação completa (ou adiar, ou carta morta) na mensagem de entrada, tudo numa operação atómica. 

### <a name="see-it-in-code"></a>Vê-lo em código

Para configurar tais transferências, cria um remetente de mensagem que visa a fila de destino através da fila de transferências. Também tem um recetor que retira mensagens da mesma fila. Por exemplo:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Uma simples transação utiliza estes elementos, como no exemplo seguinte. Para referir o exemplo completo, consulte o [código fonte no GitHub:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações sobre as filas de ônibus de serviço:

* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Entidades de ônibus de serviço de cadeia com auto-encaminhamento](service-bus-auto-forwarding.md)
* [Amostra autoavançada](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Transações Atómicas com amostra de ônibus de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Filas azure e filas de ônibus de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


