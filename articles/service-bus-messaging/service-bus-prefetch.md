---
title: Mensagens prefetch do autocarro da Azure Service | Microsoft Docs
description: Melhorar o desempenho, pré-mantendo as mensagens do Azure Service Bus. As mensagens estão prontamente disponíveis para a recuperação local antes dos pedidos de candidatura para as mesmos.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341047"
---
# <a name="prefetch-azure-service-bus-messages"></a>Mensagens de autocarro do Serviço Prefetch Azure

Quando a *Prefetch* está ativada em qualquer um dos clientes oficiais do Service Bus, o recetor adquire silenciosamente mais mensagens, até ao limite [do PrefetchCount,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) para além do que a aplicação inicialmente pedia.

Uma única chamada inicial [de Receber](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) ou [Receber Além-americana](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) adquire, portanto, uma mensagem para consumo imediato que é devolvida assim que disponível. Em seguida, o cliente adquire mais mensagens em segundo plano, para preencher o tampão pré-desprovido.

## <a name="enable-prefetch"></a>Ativar prefetch

Com .NET, ativa a função Prefetch definindo a propriedade [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) de um **MessageReceiver,** **QueueClient** ou **SubscriptionClient** para um número superior a zero. Definir o valor para zero desliga a pré-recado.

Pode adicionar facilmente esta definição ao lado de receção das definições de [amostras DePressingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) ou [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) para ver o efeito nesses contextos.

Enquanto as mensagens estão disponíveis no tampão de pré-recado, quaisquer chamadas / **ReceiveAsync** subsequentes são imediatamente preenchidas a partir do tampão, e o tampão é reabastecido em segundo plano à medida que o espaço fica disponível. Se não houver mensagens disponíveis para entrega, a operação de receção esvazia o tampão e, em seguida, espera ou bloqueia, como esperado.

A Prefetch também funciona da mesma forma com as APIs [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync.](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync)

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Se é mais rápido, por que prefetch não é a opção padrão?

O prefetch acelera o fluxo da mensagem tendo uma mensagem prontamente disponível para a recuperação local quando e antes da aplicação pedir uma. Este ganho de produção é o resultado de uma compensação que o autor da aplicação deve fazer explicitamente:

Com o modo de receção [ReceiveAndDelete,](/dotnet/api/microsoft.servicebus.messaging.receivemode) todas as mensagens adquiridas no tampão pré-fetch já não estão disponíveis na fila, e apenas residem no tampão de pré-aplicação na memória até serem recebidas na aplicação através dos  / **APIs receiveAsync** ou **OnMessage** / **OnMessage.** Se a aplicação terminar antes de as mensagens serem recebidas na aplicação, essas mensagens são irrecuperáveis perdidas.

No modo de receção [PeekLock,](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) as mensagens recolhidas no tampão Prefetch são adquiridas no tampão num estado bloqueado e têm o tempo limite para o tique-taque de bloqueio. Se o tampão pré-de primeira categoria for grande e o processamento demorar tanto tempo que as fechaduras de mensagens expiram enquanto residem no tampão pré-desproteto ou mesmo enquanto a aplicação está a processar a mensagem, pode haver alguns eventos confusos para a aplicação a manusear.

O pedido pode adquirir uma mensagem com um bloqueio caducado ou iminentemente expirado. Em caso afirmativo, a aplicação pode processar a mensagem, mas depois descobrir que não pode completá-la devido a uma expiração do bloqueio. A aplicação pode verificar a propriedade [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (que está sujeita a distorção do relógio entre o corretor e o relógio da máquina local). Se o bloqueio de mensagem tiver expirado, a aplicação deve ignorar a mensagem; nenhuma chamada da API sobre ou com a mensagem deve ser feita. Se a mensagem não tiver expirado, mas a expiração for iminente, o bloqueio pode ser renovado e prolongado por outro período de bloqueio predefinido, chamando [a mensagem. Renovar o Bloqueio()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se o bloqueio expirar silenciosamente no tampão de pré-recado, a mensagem é tratada como abandonada e é novamente disponibilizada para ser recuperada da fila. Isso pode fazer com que seja recolhido para o tampão pré-prisão; colocado no final. Se o tampão pré-de alta não puder ser normalmente trabalhado durante a expiração da mensagem, isto faz com que as mensagens sejam repetidamente pré-gravadas, mas nunca efetivamente entregues num estado utilizável (validamente bloqueado) e sejam eventualmente transferidas para a fila da letra morta uma vez que a contagem máxima de entrega seja excedida.

Se necessitar de um alto grau de fiabilidade para o processamento de mensagens, e o processamento requer trabalho e tempo significativos, recomenda-se que utilize a funcionalidade de pré-venda de forma conservadora, ou não seja de todo.

Se você precisa de alta produção e o processamento de mensagens é geralmente barato, prefetch produz benefícios de produção significativos.

A contagem máxima de pré-venda e a duração do bloqueio configurada na fila ou subscrição devem ser equilibradas de modo a que o tempo de bloqueio exceda pelo menos o tempo de processamento de mensagem esperado cumulativo para o tamanho máximo do tampão pré-detch, mais uma mensagem. Ao mesmo tempo, o tempo de bloqueio não deve ser tão longo que as mensagens possam exceder o seu tempo máximo [DeLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) quando são acidentalmente largadas, exigindo assim que o seu bloqueio expire antes de serem reentreguedas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
