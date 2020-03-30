---
title: Filas de cartas mortas do autocarro de serviço Microsoft Docs
description: Descreve filas de cartas mortas no Azure Service Bus. As filas de ônibus de serviço e as subscrições de tópicos fornecem uma subfila secundária, chamada fila de cartas mortas.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158911"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de cartas mortas do autocarro de serviço

As filas de ônibus de serviço azure e as subscrições de tópicos fornecem uma subfila secundária, chamada *fila de letras mortas* (DLQ). A fila das letras mortas não precisa de ser explicitamente criada e não pode ser eliminada ou gerida independentemente da entidade principal.

Este artigo descreve filas de cartas mortas no Autocarro de Serviço. Grande parte da discussão é ilustrada pela amostra de [filas da Dead-Letter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila da letra morta

O objetivo da fila da letra morta é guardar mensagens que não podem ser entregues a nenhum recetor, ou mensagens que não possam ser processadas. As mensagens podem então ser removidas do DLQ e inspecionadas. Uma aplicação pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registar o facto de ter havido um erro e tomar medidas corretivas. 

Do ponto de vista da API e do protocolo, o DLQ é maioritariamente semelhante a qualquer outra fila, exceto que as mensagens só podem ser submetidas através da operação de letra morta da entidade-mãe. Além disso, o tempo de viver não é observado, e não se pode escrever uma mensagem de um DLQ. A fila da letra morta suporta totalmente a entrega de bloqueio sinuoso e operações transacionais.

Não há limpeza automática do DLQ. As mensagens permanecem no DLQ até que as recupere explicitamente do DLQ e ligue para [complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem da letra morta.

## <a name="dlq-message-count"></a>Contagem de mensagens DLQ
Não é possível obter a contagem de mensagens na fila de cartas mortas a nível de tópicos. Isso é porque as mensagens não se sentam ao nível do tópico a menos que o Service Bus lance um erro interno. Em vez disso, quando um remetente envia uma mensagem para um tópico, a mensagem é reencaminhada para subscrições para o tópico dentro de milissegundos e, portanto, já não reside ao nível do tópico. Assim, pode ver mensagens no DLQ associadas à subscrição para o tópico. No exemplo seguinte, o **Service Bus Explorer** mostra que existem atualmente 62 mensagens no DLQ para a subscrição "test1". 

![Contagem de mensagens DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Também pode obter a contagem de mensagens DLQ [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)utilizando o comando Azure CLI: . 

## <a name="moving-messages-to-the-dlq"></a>Mensagens em movimento para o DLQ

Existem várias atividades no Service Bus que fazem com que as mensagens sejam empurradas para o DLQ a partir do próprio motor de mensagens. Uma aplicação também pode transferir explicitamente mensagens para o DLQ. 

À medida que a mensagem é movida pelo corretor, duas propriedades são adicionadas à mensagem `DeadLetterReason` `DeadLetterErrorDescription`à medida que o corretor chama a sua versão interna do método [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) na mensagem: e .

As aplicações podem definir os `DeadLetterReason` seus próprios códigos para a propriedade, mas o sistema define os seguintes valores.

| Condição | DeadLetterReason | Descrição de erros de carta morta |
| --- | --- | --- |
| Sempre |Tamanho do cabeçalho Ultrapassado |A quota de tamanho deste fluxo foi excedida. |
| ! Descrição do tópico.<br />Ativar Mensagens de Filtragem Antes de Publicar e SubscriçãoDescrição.<br />EnableDeadLetteringOnFilterEvaluationSExceptions |exceção. GetType(). Nome |exceção. Mensagem |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |A mensagem expirou e foi classificada como não entregue. |
| SubscriçãoDescription.RequiresSession |A identificação da sessão é nula. |A entidade com sessão ativada não permite uma mensagem cujo identificador de sessão seja nulo. |
| !fila de letras mortas | MaxTransferHopCountExceeded | O número máximo de lúpulo permitido ao encaminhar entre as filas. O valor está definido para 4. |
| Inscrição explicitada letras mortas |Especificado por aplicação |Especificado por aplicação |

## <a name="exceeding-maxdeliverycount"></a>Excedendo a Contagem máxima

As filas e subscrições têm cada uma uma a [queueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) propriedade respectivamente; o valor padrão é 10. Sempre que uma mensagem tiver sido entregue sob um bloqueio[(ReceiveMode.PeekLock),](/dotnet/api/microsoft.azure.servicebus.receivemode)mas tiver sido explicitamente abandonada ou o bloqueio tiver expirado, a mensagem [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) é incrementada. Quando o [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) excede o [MaxDeliveryCount,](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)a mensagem é `MaxDeliveryCountExceeded` transferida para o DLQ, especificando o código da razão.

Este comportamento não pode ser desativado, mas pode definir [o MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) para um grande número.

## <a name="exceeding-timetolive"></a>Excesso de TempoPara Viver

Quando a Descrição da [Fila.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriçãoDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) property is **set to true** (o predefinido é **falso**), todas as mensagens de validade são transferidas para o DLQ, especificando o `TTLExpiredException` código de razão.

As mensagens expiradas só são purgadas e transferidas para o DLQ quando há pelo menos um recetor ativo a puxar da fila principal ou da subscrição; que o comportamento é por design.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar regras de subscrição

Quando a [subscriçãoDescription.EnableDeadLetteringOnFilterEvaluationA](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) propriedade está ativada para uma subscrição, quaisquer erros que ocorram enquanto a regra do filtro SQL de uma subscrição executa são capturadas no DLQ juntamente com a mensagem ofensiva.

## <a name="application-level-dead-lettering"></a>Letras mortas ao nível da aplicação

Além das funcionalidades de letras mortas fornecidas pelo sistema, as aplicações podem usar o DLQ para rejeitar explicitamente mensagens inaceitáveis. Podem incluir mensagens que não podem ser processadas corretamente devido a qualquer tipo de problema do sistema, mensagens que possuam cargas mal formadas ou mensagens que falham na autenticação quando é utilizado algum esquema de segurança ao nível da mensagem.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Letras mortas em cenários ForwardTo ou SendVia

As mensagens serão enviadas para a fila de cartas mortas de transferência nas seguintes condições:

- Uma mensagem passa por mais de quatro filas ou tópicos que estão [acorrentados.](service-bus-auto-forwarding.md)
- A fila de destino ou tópico é desativado ou eliminado.
- A fila de destino ou tópico excede o tamanho máximo da entidade.

Para recuperar estas mensagens com letras mortas, pode criar um recetor utilizando o método de utilidade [FormatTransferDeadletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Exemplo

O seguinte código de corte cria um recetor de mensagem. No circuito de receção para a fila principal, o código recupera a mensagem com [o Receive (TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que pede ao corretor que devolva instantaneamente qualquer mensagem prontamente disponível, ou que regresse sem resultado. Se o código receber uma mensagem, abandona-a imediatamente, o que aumenta o `DeliveryCount`. Uma vez que o sistema move a mensagem para o DLQ, a fila principal está vazia e o loop sai, uma vez que [o ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) devolve **nulos**.

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

Se estiver a utilizar o .NET SDK, pode obter o caminho para a fila de letras mortas utilizando o método SubscriptionClient.FormatDeadLetterPath(). Este método tem o nome tópico/nome de subscrição e sufixos com **/$DeadLetterQueue**.


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações sobre as filas de ônibus de serviço:

* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Filas azure e filas de ônibus de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

