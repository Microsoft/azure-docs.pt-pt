---
title: Filas de mensagens mortas do barramento de serviço | Microsoft Docs
description: Visão geral das filas de mensagens mortas do barramento de serviço do Azure
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
ms.openlocfilehash: 79bc5e640498788ef805d07a26dd29e943117b58
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476965"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de mensagens mortas do barramento de serviço

As filas do barramento de serviço do Azure e as assinaturas de tópico fornecem uma subfila secundária, chamada de DLQ ( *fila de mensagens mortas* ). A fila de mensagens mortas não precisa ser criada explicitamente e não pode ser excluída ou gerenciada de outra forma independentemente da entidade principal.

Este artigo descreve as filas de mensagens mortas no barramento de serviço. Grande parte da discussão é ilustrada pelo [exemplo de filas de mensagens mortas](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no github.
 
## <a name="the-dead-letter-queue"></a>A fila de mensagens mortas

A finalidade da fila de mensagens mortas é manter as mensagens que não podem ser entregues a nenhum receptor ou mensagens que não puderam ser processadas. As mensagens podem ser removidas do DLQ e inspecionadas. Um aplicativo pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registrar o fato de que houve um erro e tomar uma ação corretiva. 

De uma perspectiva de API e protocolo, o DLQ é principalmente semelhante a qualquer outra fila, exceto que as mensagens só podem ser enviadas por meio da operação de mensagens mortas da entidade pai. Além disso, o tempo de vida não é observado, e você não pode mensagens mortas de um DLQ. A fila de mensagens mortas dá suporte total às operações transacionais e de entrega de bloqueio de inspeção.

Observe que não há nenhuma limpeza automática do DLQ. As mensagens permanecem no DLQ até você recuperá-las explicitamente do DLQ e chamar [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem de mensagens mortas.

## <a name="moving-messages-to-the-dlq"></a>Movendo mensagens para o DLQ

Há várias atividades no barramento de serviço que fazem com que as mensagens sejam enviadas por push para o DLQ de dentro do próprio mecanismo de mensagens. Um aplicativo também pode mover mensagens explicitamente para o DLQ. 

À medida que a mensagem é movida pelo agente, duas propriedades são adicionadas à mensagem, pois o agente chama sua versão interna [do método](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) inativa na mensagem `DeadLetterReason` : `DeadLetterErrorDescription`e.

Os aplicativos podem definir seus próprios códigos para `DeadLetterReason` a propriedade, mas o sistema define os valores a seguir.

| Condição | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Sempre |HeaderSizeExceeded |A quota de tamanho deste fluxo foi excedida. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing e SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |A mensagem expirou e foi classificada como não entregue. |
| SubscriptionDescription.RequiresSession |A ID da sessão é nula. |A entidade com sessão ativada não permite uma mensagem cujo identificador de sessão seja nulo. |
| ! fila de mensagens mortas | MaxTransferHopCountExceeded | O número máximo de saltos permitidos ao encaminhar entre filas. O valor é definido como 4. |
| Mensagens mortas explícitas do aplicativo |Especificado pelo aplicativo |Especificado pelo aplicativo |

## <a name="exceeding-maxdeliverycount"></a>Excedendo MaxDeliveryCount

Filas e assinaturas têm, cada uma, uma propriedade [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) , respectivamente; o valor padrão é 10. Sempre que uma mensagem tiver sido entregue sob um bloqueio ([receivemode. Peeklock](/dotnet/api/microsoft.azure.servicebus.receivemode)), mas tiver sido explicitamente abandonada ou o bloqueio tiver expirado, a mensagem [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) será incrementada. Quando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) excede [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), a mensagem é movida para o DLQ, especificando o `MaxDeliveryCountExceeded` código do motivo.

Esse comportamento não pode ser desabilitado, mas você pode definir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) como um número muito grande.

## <a name="exceeding-timetolive"></a>Excedendo TimeToLive

Quando a propriedade [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) é definida como **true** (o padrão é **false**), todas as mensagens expirando são movido para o DLQ, especificando o `TTLExpiredException` código do motivo.

Observe que as mensagens expiradas são limpas apenas e movidas para o DLQ quando há pelo menos um receptor ativo recebendo da fila principal ou assinatura; Esse comportamento é por design.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar regras de assinatura

Quando a propriedade [SubscriptionDescription. habilitarmensagensmortasnaexceçãodeavaliaçãonofiltro](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) estiver habilitada para uma assinatura, todos os erros que ocorrerem durante a execução de uma regra de filtro SQL de uma assinatura serão capturados no DLQ junto com o incorreto Mensagem.

## <a name="application-level-dead-lettering"></a>Mensagens mortas no nível do aplicativo

Além dos recursos de mensagens mortas fornecidos pelo sistema, os aplicativos podem usar o DLQ para rejeitar explicitamente as mensagens inaceitáveis. Isso pode incluir mensagens que não podem ser processadas corretamente devido a qualquer tipo de problema do sistema, mensagens que mantêm cargas malformadas ou mensagens que falham na autenticação quando algum esquema de segurança no nível da mensagem é usado.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mensagens mortas nos cenários de Encaminhato ou SendVia

As mensagens serão enviadas para a fila de mensagens mortas de transferência sob as seguintes condições:

- Uma mensagem passa por mais de 4 filas ou tópicos que são encadeados [juntos](service-bus-auto-forwarding.md).
- A fila ou o tópico de destino está desabilitado ou excluído.
- A fila ou o tópico de destino excede o tamanho máximo da entidade.

Para recuperar essas mensagens mortas, você pode criar um receptor usando o método utilitário [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) .

## <a name="example"></a>Exemplo

O trecho de código a seguir cria um receptor de mensagem. No loop de recebimento da fila principal, o código recupera a mensagem com [Receive (TimeSpan. zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que solicita que o agente retorne instantaneamente qualquer mensagem prontamente disponível ou para retornar sem nenhum resultado. Se o código receber uma mensagem, ele a abandonará imediatamente, o que incrementa o `DeliveryCount`. Depois que o sistema move a mensagem para o DLQ, a fila principal está vazia e o loop é encerrado, como [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) retorna **NULL**.

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

## <a name="path-to-the-dead-letter-queue"></a>Caminho para a fila de mensagens mortas
Você pode acessar a fila de mensagens mortas usando a seguinte sintaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscription/<subscription path>/$deadletterqueue
```

Se você estiver usando o SDK do .NET, poderá obter o caminho para a fila de mensagens mortas usando o método SubscriptionClient. FormatDeadLetterPath (). Esse método usa o nome do tópico/assinatura e sufixos com **/$DeadLetterQueue**.


## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para obter mais informações sobre filas do barramento de serviço:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Filas do Azure e filas do barramento de serviço comparadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

