---
title: Mensagens de pré-fetch do Azure Service Bus / Microsoft Docs
description: Melhore o desempenho ao pré-rebuscar as mensagens do Ônibus do Serviço Azure. As mensagens estão prontamente disponíveis para recuperação local antes dos pedidos de inscrição para as mesmas.
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
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760662"
---
# <a name="prefetch-azure-service-bus-messages"></a>Mensagens de ônibus de serviço Prefetch Azure

Quando a *Prefetch* é ativada em qualquer um dos clientes oficiais do Service Bus, o recetor adquire silenciosamente mais mensagens, até ao limite [prefetchCount,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) para além do que a aplicação inicialmente pedia.

Uma única chamada inicial [de Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) ou [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) adquire, portanto, uma mensagem para consumo imediato que é devolvida logo que disponível. Em seguida, o cliente adquire mais mensagens em segundo plano, para preencher o tampão de prefetch.

## <a name="enable-prefetch"></a>Ativar prefetch

Com .NET, ativa a funcionalidade Prefetch definindo a propriedade [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) de um **MessageReceiver,** **QueueClient,** ou **SubscriptionClient** para um número superior a zero. Definir o valor para zero desliga a pré-busca.

Pode facilmente adicionar esta definição ao lado recebido das definições das [amostras QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) ou [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) para ver o efeito nesses contextos.

Enquanto as mensagens estão disponíveis no tampão de pré-busca, quaisquer**chamadas** **recebidas**/subsequentes são imediatamente preenchidas a partir do tampão, e o tampão é reabastecido em segundo plano à medida que o espaço fica disponível. Se não houver mensagens disponíveis para entrega, a operação de receção esvazia o tampão e, em seguida, espera ou bloqueia, como esperado.

A Prefetch também funciona da mesma forma com as APIs [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync.](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync)

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Se é mais rápido, porque é que a Prefetch não é a opção por defeito?

A Prefetch acelera o fluxo de mensagens, tendo uma mensagem prontamente disponível para a recuperação local quando e antes da aplicação pedir uma. Este ganho de entrada é o resultado de uma compensação que o autor da aplicação deve fazer explicitamente:

Com o modo de receção [E Exclusão,](/dotnet/api/microsoft.servicebus.messaging.receivemode) todas as mensagens adquiridas no tampão de pré-busca já não estão disponíveis na fila e só residem no tampão de pré-rebusca na memória até serem recebidas na aplicação através **das**/APIs**ReceiveAsync** ou**OnMessage OnMessageAsync.** **OnMessage**/ Se o pedido terminar antes de as mensagens serem recebidas na aplicação, essas mensagens perder-se-ão irremediávelmente.

No modo de receção [PeekLock,](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) as mensagens recolhidas no tampão Prefetch são adquiridas no tampão em estado fechado e têm o relógio de tempo limite para o tique-taque do bloqueio. Se o tampão de pré-fetch for grande, e o processamento demorar tanto tempo que os bloqueios de mensagens expiram enquanto residem no tampão de pré-busca ou mesmo enquanto a aplicação está a processar a mensagem, pode haver alguns eventos confusos para a aplicação manusear.

O pedido pode adquirir uma mensagem com um cadeado expirado ou iminentemente expirado. Em caso afirmativo, a aplicação poderá processar a mensagem, mas depois descobrir que não pode completá-la devido a uma expiração do bloqueio. A aplicação pode verificar a propriedade [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (que está sujeita a um desvio do relógio entre o corretor e o relógio da máquina local). Se o bloqueio de mensagem tiver expirado, o pedido deve ignorar a mensagem; nenhuma chamada aPi ou com a mensagem deve ser feita. Se a mensagem não estiver expirada, mas a expiração estiver iminente, o bloqueio pode ser renovado e prolongado por outro período de bloqueio predefinido, ligando [para a mensagem. Renovar o Cadeado()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se o cadeado expirar silenciosamente no tampão de pré-rebusca, a mensagem é tratada como abandonada e é novamente disponibilizada para recuperação da fila. Isso pode fazer com que seja recolhido para o tampão de prebusca; colocado no final. Se o tampão de prefetch não pode ser normalmente trabalhado durante a expiração da mensagem, isto faz com que as mensagens sejam repetidamente prefetched mas nunca efetivamente entregues em um estado utilizável (validamente bloqueado) e são eventualmente movidos para a fila de letras mortas uma vez que o A contagem máxima de entrega é ultrapassada.

Se necessitar de um elevado grau de fiabilidade para o processamento de mensagens, e o processamento requer um trabalho e tempo significativos, recomenda-se que utilize a funcionalidade de pré-busca de forma conservadora, ou não.

Se precisar de alta produção e o processamento de mensagens é geralmente barato, a prefetch produz benefícios significativos de produção.

A contagem máxima de prefetch e a duração do bloqueio configurada na fila ou subscrição devem ser equilibradas de modo a que o tempo limite de bloqueio exceda pelo menos o tempo de processamento de mensagens esperado cumulativo para o tamanho máximo do tampão de pré-rebusca, mais uma mensagem. Ao mesmo tempo, o tempo limite de bloqueio não deve ser tão longo que as mensagens possam exceder o seu [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) máximo quando são acidentalmente largadas, exigindo assim que o seu bloqueio expire antes de serem reentregues.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
