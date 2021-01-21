---
title: Filas de cartas mortas do Autocarro de Serviço | Microsoft Docs
description: Descreve filas de cartas mortas no Azure Service Bus. As filas de autocarros de serviço e as subscrições de tópicos fornecem um subqueue secundário, chamado de fila de letras mortas.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630103"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de cartas mortas do Service Bus

As filas de autocarros da Azure Service e as subscrições de tópicos fornecem um subqueue secundário, chamado de *fila de letras mortas* (DLQ). A fila de cartas mortas não precisa de ser explicitamente criada e não pode ser eliminada ou gerida de outra forma independentemente da entidade principal.

Este artigo descreve filas de cartas mortas no Service Bus. Grande parte da discussão é ilustrada pela amostra das filas da [Carta Morta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila da carta morta

O objetivo da fila da carta morta é guardar mensagens que não podem ser entregues a nenhum recetor, ou mensagens que não possam ser processadas. As mensagens podem então ser removidas do DLQ e inspecionadas. Uma aplicação pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registar o facto de que houve um erro e tomar medidas corretivas. 

Do ponto de vista da API e do protocolo, o DLQ é maioritariamente semelhante a qualquer outra fila, exceto que as mensagens só podem ser enviadas através da operação de letra morta da entidade-mãe. Além disso, o tempo de viver não é observado, e não se pode escrever uma mensagem de um DLQ. A fila da carta morta suporta totalmente a entrega de espremiações e operações transacionais.

Não há limpeza automática do DLQ. As mensagens permanecem no DLQ até que as recupere explicitamente do DLQ e ligue para [a Complete na](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) mensagem da letra morta.

## <a name="dlq-message-count"></a>Contagem de mensagens DLQ
Não é possível obter a contagem de mensagens na fila das letras mortas ao nível do tópico. Isso é porque as mensagens não se sentam ao nível do tópico a menos que o Service Bus atire um erro interno. Em vez disso, quando um remetente envia uma mensagem para um tópico, a mensagem é reencaminhada para subscrições para o tema dentro de milissegundos e, portanto, já não reside ao nível do tópico. Assim, pode ver mensagens no DLQ associadas à subscrição do tópico. No exemplo seguinte, o **Service Bus Explorer** mostra que existem atualmente 62 mensagens no DLQ para a subscrição "test1". 

![Contagem de mensagens DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Também pode obter a contagem de mensagens DLQ utilizando o comando Azure CLI: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Mensagens de deslocação para o DLQ

Existem várias atividades no Service Bus que fazem com que as mensagens sejam empurradas para o DLQ a partir do próprio motor de mensagens. Uma aplicação também pode mover explicitamente mensagens para o DLQ. 

À medida que a mensagem é movida pelo corretor, duas propriedades são adicionadas à mensagem, uma vez que o corretor chama a sua versão interna do método [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) na mensagem: `DeadLetterReason` e `DeadLetterErrorDescription` .

As aplicações podem definir os seus próprios códigos para a `DeadLetterReason` propriedade, mas o sistema define os seguintes valores.

| DeadLetterReason | DeadLetterrorDescription |
| --- | --- |
|HeaderSizeExceed |A quota de tamanho deste fluxo foi excedida. |
|TTLExpiredException |A mensagem expirou e foi classificada como não entregue. Consulte a secção [TimeToLive excedendo](#exceeding-timetolive) para obter mais detalhes. |
|A identificação da sessão é nula. |A entidade com sessão ativada não permite uma mensagem cujo identificador de sessão seja nulo. |
|MaxTransferHopCountExceed | O número máximo de lúpulo permitido ao encaminhar entre filas. O valor está definido para 4. |
| MaxDeliveryCountExceedededEdEdExceptionMessage | A mensagem não podia ser consumida após as tentativas máximas de entrega. Consulte a secção [MaxDeliveryCount excedendo](#exceeding-maxdeliverycount) para mais detalhes. |

## <a name="exceeding-maxdeliverycount"></a>Excedendo MaxDeliveryCount

As filas e subscrições têm uma propriedade [queueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriçãoDescription.MaxDeliveryCount,](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) respectivamente; o valor predefinido é 10. Sempre que uma mensagem tiver sido entregue sob um bloqueio[(ReceiveMode.PeekLock),](/dotnet/api/microsoft.azure.servicebus.receivemode)mas tenha sido explicitamente abandonada ou a fechadura expirada, a mensagem [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) é incrementada. Quando [o DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) excede o [MaxDeliveryCount,](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)a mensagem é transferida para o DLQ, especificando o `MaxDeliveryCountExceeded` código de razão.

Este comportamento não pode ser desativado, mas pode definir [o MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) num grande número.

## <a name="exceeding-timetolive"></a>Excedendo o TempoToLive

Quando a propriedade [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) é definida como **verdadeira** (o padrão é **falso),** todas as mensagens caduques são transferidas para o DLQ, especificando o  `TTLExpiredException` código de razão.

As mensagens expiradas só são purgadas e transferidas para o DLQ quando há pelo menos um recetor ativo a puxar da fila principal ou subscrição, e [as mensagens diferidas](./message-deferral.md) também não serão purgadas e transferidas para a fila da letra morta após o seu termo. Estes comportamentos são por desígnio.

## <a name="errors-while-processing-subscription-rules"></a>Erros durante o processamento das regras de subscrição

Quando a propriedade [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) é ativada para uma subscrição, quaisquer erros que ocorram enquanto a regra do filtro SQL de uma subscrição executa são capturados no DLQ juntamente com a mensagem ofensiva. Não utilize esta opção num ambiente de produção em que nem todos os tipos de mensagens tenham assinantes.

## <a name="application-level-dead-lettering"></a>Letras mortas ao nível da aplicação

Além das funcionalidades de letras mortas fornecidas pelo sistema, as aplicações podem usar o DLQ para rejeitar explicitamente mensagens inaceitáveis. Podem incluir mensagens que não podem ser processadas corretamente devido a qualquer tipo de problema do sistema, mensagens que possuam cargas mal formadas ou mensagens que falham na autenticação quando é utilizado um esquema de segurança ao nível da mensagem.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Letras mortas em cenários ForwardTo ou SendVia

As mensagens serão enviadas para a fila de transferência de cartas mortas nas seguintes condições:

- Uma mensagem passa por mais de quatro filas ou tópicos que estão [acorrentados em conjunto.](service-bus-auto-forwarding.md)
- A fila de destino ou tópico é desativado ou eliminado.
- A fila de destino ou tópico excede o tamanho máximo da entidade.

Para recuperar estas mensagens com letras mortas, pode criar um recetor utilizando o método de utilitário [FormatTransferDletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Exemplo

O seguinte corte de código cria um recetor de mensagens. No circuito de receção para a fila principal, o código recupera a mensagem com [Receive (TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que pede ao corretor que devolva instantaneamente qualquer mensagem prontamente disponível ou que regresse sem resultado. Se o código receber uma mensagem, abandona-a imediatamente, o que aumenta o  `DeliveryCount` . Uma vez que o sistema move a mensagem para o DLQ, a fila principal está vazia e o loop sai, uma vez [que o ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) retorna **nulo**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Caminho para a fila da letra morta
Pode aceder à fila da letra morta utilizando a seguinte sintaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Se estiver a utilizar o .NET SDK, pode obter o caminho para a fila de letras mortas utilizando o método SubscriptionClient.FormatDeadLetterPath(). Este método tem o nome de tópico/nome de subscrição e sufixa com **/$DeadLetterQueue**.


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações sobre as filas do Service Bus:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Filas de Azure e filas de autocarros de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

