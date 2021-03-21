---
title: Visão geral do processamento de transações no Azure Service Bus
description: Este artigo dá-lhe uma visão geral do processamento de transações e o envio através de recurso no Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2848f41d5557584b0f1a197b548a00a4aef1564
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183748"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Visão geral do processamento de transações de autocarros de serviço

Este artigo discute as capacidades de transação da Microsoft Azure Service Bus. Grande parte da discussão é ilustrada pelas [Transações AMQP com a amostra de Service Bus.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) Este artigo limita-se a uma visão geral do processamento de transações e o *envio através de* funcionalidades no Service Bus, enquanto a amostra de Transações Atómicas é mais ampla e complexa no âmbito.

> [!NOTE]
> O nível básico da Service Bus não suporta transações. Os níveis standard e premium suportam as transações. Para obter diferenças entre estes níveis, consulte [os preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="transactions-in-service-bus"></a>Transações em Service Bus

Uma *transação* agrupo duas ou mais operações juntas num *âmbito de execução.* Por natureza, tal transação deve assegurar que todas as operações pertencentes a um determinado grupo de operações tenham êxito ou falhem conjuntamente. A este respeito, as transações funcionam como uma unidade, que é frequentemente referida como *atomicidade.*

O Service Bus é um corretor de mensagens transacionais e garante a integridade transacional de todas as operações internas contra as suas lojas de mensagens. Todas as transferências de mensagens dentro do Service Bus, como a transferência de mensagens para uma [fila de cartas mortas](service-bus-dead-letter-queues.md) ou [o encaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre entidades, são transacionais. Como tal, se a Service Bus aceitar uma mensagem, já foi armazenada e rotulada com um número de sequência. A partir daí, quaisquer transferências de mensagens dentro da Service Bus são operações coordenadas entre entidades, e não conduzirão a perdas (fonte bem sucedida e falha o alvo) ou à duplicação (falha de origem e o alvo sucede) da mensagem.

O Service Bus suporta operações de agrupamento em relação a uma entidade de mensagens única (fila, tópico, subscrição) no âmbito de uma transação. Por exemplo, pode enviar várias mensagens para uma fila dentro de um âmbito de transação, e as mensagens só serão comprometidas no registo da fila quando a transação estiver concluída com sucesso.

## <a name="operations-within-a-transaction-scope"></a>Operações dentro de um âmbito de transação

As operações que podem ser realizadas dentro de um âmbito de transação são as seguintes:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender,](/dotnet/api/microsoft.azure.servicebus.core.messagesender) [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` `SendBatch` ,`SendBatchAsync`
* **[IntermediárioS:](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** `Complete` , , `CompleteAsync` , , , `Abandon` , , , , , `AbandonAsync` `Deadletter` `DeadletterAsync` `Defer` `DeferAsync` `RenewLock` , `RenewLockAsync` 

As operações de receção não estão incluídas, pois presume-se que a aplicação adquire mensagens utilizando o modo [ReceberMode.PeekLock,](/dotnet/api/microsoft.azure.servicebus.receivemode) dentro de alguns loops de receção ou com uma chamada [OnMessage,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e só então abre uma margem de transação para o processamento da mensagem.

A disposição da mensagem (completa, abandono, letra morta, adiamento) ocorre então no âmbito e dependente do resultado global da transação.

## <a name="transfers-and-send-via"></a>Transferências e "enviar via"

Para permitir a transferência transacional de dados de uma fila ou tópico para um processador e, em seguida, para outra fila ou tópico, o Service Bus suporta *transferências.* Numa operação de transferência, um remetente envia primeiro uma mensagem para uma *fila de transferência ou tópico*, e a fila de transferência ou tópico move imediatamente a mensagem para a fila de destino pretendida ou tópico usando a mesma implementação de transferência robusta em que a capacidade de autoforward depende. A mensagem nunca é comprometida com a fila de transferência ou o login do tópico de forma a tornar-se visível para a fila de transferências ou para os consumidores do tópico.

A potência desta capacidade transacional torna-se evidente quando a fila de transferência ou o tópico em si é a fonte das mensagens de entrada do remetente. Por outras palavras, o Service Bus pode transferir a mensagem para a fila de destino ou tópico "via" a fila ou tópico de transferência, enquanto realiza uma operação completa (ou adiar, ou letra morta) na mensagem de entrada, tudo numa operação atómica. 

Se precisar de receber de uma subscrição de tópicos e depois enviar para uma fila ou tópico na mesma transação, a entidade de transferência deve ser um tópico. Neste cenário, inicie o âmbito de transação sobre o tema, receba a partir da subscrição com o âmbito de transação, e envie através do tópico de transferência para uma fila ou destino tópico. 

### <a name="see-it-in-code"></a>Vê-lo em código

Para configurar tais transferências, cria um remetente de mensagens que visa a fila de destino através da fila de transferências. Também tem um recetor que retira mensagens dessa mesma fila. Por exemplo:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Uma simples transação utiliza então estes elementos, como no exemplo seguinte. Para remeter o exemplo completo, consulte o [código fonte no GitHub:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

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

## <a name="timeout"></a>Tempo Limite
Uma transação acaba após 2 minutos. O temporizador de transações começa quando a primeira operação da transação começa. 

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações sobre as filas do Service Bus:

* [Como utilizar as filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Entidades de autocarros de serviço de chaining com autoforwarding](service-bus-auto-forwarding.md)
* [Amostra de autoforal](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Transações atómicas com amostra de ônibus de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Filas de Azure e filas de autocarros de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


