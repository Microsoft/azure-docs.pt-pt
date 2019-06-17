---
title: Filas de mensagens não entregues do Service Bus | Documentos da Microsoft
description: Descrição geral de filas de mensagens não entregues do Azure Service Bus
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
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: af67b27dacf3bb86c2dd5c878a2751e027a53acb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003123"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Descrição geral das filas de mensagens não entregues do Service Bus

As filas do Service Bus do Azure e subscrições de tópicos fornecem uma fila secundária secundária, chamada um *fila de mensagens não entregues* (DLQ). A fila de mensagens não entregues não precisa de ser explicitamente criado e não pode ser eliminado ou geridos caso contrário, independente da entidade principal.

Este artigo descreve as filas de mensagens não entregues no Service Bus. Grande parte da discussão é ilustrada pela [exemplo de filas de mensagens não entregues](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila de mensagens não entregues

O objetivo da fila de mensagens não entregues é conter as mensagens que não não possível entregar a qualquer recetor ou mensagens que não foi possível processar. As mensagens, em seguida, podem ser removidas o DLQ e inspecioná-lo. Uma aplicação pode, com a ajuda de um operador, corrigir problemas e volte a submeter a mensagem, o fato de que ocorreu um erro de registo e tomar medidas corretivas. 

Da perspectiva de API e o protocolo, o DLQ principalmente é semelhante a qualquer outra fila, exceto pelo fato de mensagens só podem ser submetidas através da operação de mensagens não entregues da entidade principal. Além disso, a time-to-live não é observada, e não é possível entregar uma mensagem a partir de um DLQ. A fila de mensagens não entregues totalmente compatível com operações transacionais e a entrega de bloqueio de pré-visualização.

Tenha em atenção que não existe nenhuma a limpeza automática do DLQ. Mensagens permanecem no DLQ até obter explicitamente da chamada e DLQ [concluir ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem de mensagens não entregues.

## <a name="moving-messages-to-the-dlq"></a>Passar mensagens para o DLQ

Existem várias atividades no Service Bus com que as mensagens obter enviada por push para o DLQ de dentro do próprio mecanismo de mensagens. Um aplicativo também explicitamente pode mover as mensagens para o DLQ. 

Como a mensagem é movida pelo broker, duas propriedades são adicionadas à mensagem, como o Mediador de chamadas à sua versão interna dos [mensagens não entregues](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) método da mensagem: `DeadLetterReason` e `DeadLetterErrorDescription`.

Aplicativos podem definir seus próprios códigos para o `DeadLetterReason` propriedade, mas o sistema define os seguintes valores.

| Condição | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Sempre |HeaderSizeExceeded |A quota de tamanho deste fluxo foi excedida. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing e SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |A mensagem expirou e foi classificada como não entregue. |
| SubscriptionDescription.RequiresSession |Id de sessão é nulo. |A entidade com sessão ativada não permite uma mensagem cujo identificador de sessão seja nulo. |
| ! entregues fila |MaxTransferHopCountExceeded |Null |
| Aplicação explícita lettering inutilizado |Especificado por aplicativo |Especificado por aplicativo |

## <a name="exceeding-maxdeliverycount"></a>MaxDeliveryCount exceder

Filas e subscrições de ter uma [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) propriedade respectivamente; o valor predefinido é 10. Sempre que uma mensagem foi entregue num bloqueio ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), mas tem sido um explicitamente abandonada ou o bloqueio tiver expirado, a mensagem [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) é incrementado. Quando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) excede [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), a mensagem é movida para o DLQ, especificando o `MaxDeliveryCountExceeded` código de razão.

Não é possível desativar esse comportamento, mas pode definir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) para um número muito grande.

## <a name="exceeding-timetolive"></a>TimeToLive exceder

Quando o [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) estiver definida como **true** (a predefinição é **false**), todas as mensagens prestes a expirar são movidas para o DLQ, especificando o `TTLExpiredException` código de razão.

Tenha em atenção que mensagens expiradas são apenas removidas e movidas para o DLQ quando existe pelo menos um recetor ativo, extrair a partir da fila de principal ou a subscrição; esse comportamento é propositado.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar as regras de subscrição

Quando o [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) propriedade está ativada para uma subscrição, quaisquer erros que ocorrem enquanto executa a regra de filtro SQL de uma subscrição são capturados na DLQ ao longo com a mensagem incorreto.

## <a name="application-level-dead-lettering"></a>Nível de aplicativo de mensagens não entregues

Além das funcionalidades de mensagens não entregues fornecidos pelo sistema, os aplicativos podem usar o DLQ explicitamente rejeite mensagens inaceitáveis. Isto pode incluir as mensagens que não não possível processar corretamente devido a qualquer tipo de problema no sistema, as mensagens que contêm payloads com formato incorreto ou mensagens que falham a autenticação quando é utilizada um esquema de segurança de nível de mensagem.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mensagens não entregues em cenários de ForwardTo ou SendVia

As mensagens serão enviadas para a fila de mensagens não entregues de transferência nas seguintes condições:

- Uma mensagem passa por mais de 4 filas ou tópicos que são [encadeadas](service-bus-auto-forwarding.md).
- A fila de destino ou um tópico, está desativado ou eliminado.
- A fila de destino ou um tópico excede o tamanho máximo de entidade.

Para obter essas mensagens lettered em papel já era, pode criar um recetor utilizando o [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) método utilitário.

## <a name="example"></a>Exemplo

O fragmento de código seguinte cria um recetor de mensagem. No loop de recebimento para a fila principal, o código obtém a mensagem com [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que pede o mediador instantaneamente retornar qualquer mensagem prontamente disponível ou a devolver com nenhum resultado. Se o código de receber uma mensagem, ele imediatamente abandona-lo, que incrementa a `DeliveryCount`. Depois do sistema move a mensagem para o DLQ, a fila principal está vazia e sai do loop, como [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) devolve **nulo**.

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

## <a name="path-to-the-dead-letter-queue"></a>Caminho para a fila de mensagens não entregues
Pode aceder a fila de mensagens não entregues, utilizando a seguinte sintaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscription/<subscription path>/$deadletterqueue
```

Se estiver a utilizar o SDK de .NET, pode obter o caminho para a fila de mensagens não entregues ao utilizar o método SubscriptionClient.FormatDeadLetterPath(). Esse método usa o nome de nome/subscrição de tópico e sufixos com **/$DeadLetterQueue**.


## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes para obter mais informações sobre as filas do Service Bus:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Filas do Azure e do Service Bus filas em comparação comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

