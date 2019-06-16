---
title: Mensagens de obtenção prévia do Service Bus do Azure | Documentos da Microsoft
description: Melhore o desempenho ao pré-busca mensagens do Service bus do Azure.
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
ms.date: 08/30/2018
ms.author: aschhab
ms.openlocfilehash: c63e6bf66e4832a1a5b0b5e6fc3dfbbf02d1e490
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125853"
---
# <a name="prefetch-azure-service-bus-messages"></a>Obtenção prévia de mensagens do Service bus do Azure

Quando *obtenção prévia* está ativada em qualquer um dos clientes do Service Bus oficiais, o receptor no modo silencioso adquire mais mensagens, até o [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) limite, para além do que o aplicativo inicialmente pediu.

Um único inicial [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) ou [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) chamada, por conseguinte, adquire uma mensagem para consumo imediata devolvido assim como disponível. O cliente adquire mais mensagens em segundo plano, para preencher o buffer de obtenção prévia.

## <a name="enable-prefetch"></a>Ativar obtenção prévia

Com o .NET, é possível ativar a funcionalidade de obtenção prévia, definindo a [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) propriedade de um **MessageReceiver**, **QueueClient**, ou **SubscriptionClient**  para um número maior que zero. Definindo o valor como zero desliga obtenção prévia.

Pode adicionar facilmente esta definição para o lado de receção do [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) ou [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) definições dos exemplos para ver o efeito nesses contextos.

Enquanto as mensagens estão disponíveis no buffer da obtenção prévia, qualquer subsequente **Receive**/**ReceiveAsync** chamadas são atendidas imediatamente o buffer e a memória intermédia é reposta no em segundo plano como espaço se tornar disponível. Se não houver nenhuma mensagem disponível para entrega, a operação receive esvazia a memória intermédia e, em seguida, aguarda ou blocos, conforme o esperado.

Obtenção prévia também funciona da mesma forma com o [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) APIs.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Se for mais rápida, por que motivo obtenção prévia não é a opção padrão?

Obtenção prévia acelera o fluxo de mensagens ao ter uma mensagem prontamente disponível para recuperação local, quando e antes da aplicação pede-lhe um. Esse benefício de débito é o resultado de uma contrapartida que o autor da aplicação tem de certificar explicitamente:

Com o [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) receber modo, todas as mensagens que são adquiridas para o buffer de obtenção prévia já não estão disponíveis na fila e apenas residem na memória intermédia de obtenção prévia na memória até serem recebidas na aplicação através da **receber**/**ReceiveAsync** ou **OnMessage**/**OnMessageAsync** APIs. Se o aplicativo é encerrado antes das mensagens são recebidas no aplicativo, essas mensagens são perdidas de forma irrevogável.

Na [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) receber modo, as mensagens obtidas para o buffer de obtenção prévia são adquiridas para o buffer no estado bloqueado e tem o período de tempo limite para o bloqueio a funcionar corretamente. Se a memória intermédia de obtenção prévia é grande, e o processamento demorar tanto tempo que a mensagem bloqueios expirarem ao mesmo tempo que reside na memória intermédia de obtenção prévia ou até mesmo enquanto a aplicação está a processar a mensagem, pode haver alguns eventos confusos para a aplicação processar.

O aplicativo poderá adquirir uma mensagem com um bloqueio expirado ou imminently prestes a expirar. Se assim for, a aplicação pode processar a mensagem, mas, em seguida, encontre o que ele não é possível concluí-la devido à expiração de um bloqueio. A aplicação pode verificar o [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) propriedade (que está sujeito a distorção entre o agente e o relógio do computador local). Se o bloqueio da mensagem tiver expirado, o aplicativo deve ignorar a mensagem; nenhuma chamada de API no ou com a mensagem deve ser feita. Se a mensagem não expirou, mas expiração é iminente, o bloqueio pode ser renovado e estendido por outro período de bloqueio do padrão chamando [mensagem. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se o bloqueio expirar silenciosamente na memória intermédia de obtenção prévia, a mensagem é tratada como abandonada e novamente é disponibilizada para a obtenção da fila. Que pode fazer com que ele ser buscada na memória intermédia de obtenção prévia; colocados no final. Se a memória intermédia de obtenção prévia, normalmente, não pode ser trabalhada com durante a expiração de mensagem, isso faz com que as mensagens sejam repetidamente pré-obtidos porém entregues nunca efetivamente num estado utilizável (legitimamente bloqueado) e, eventualmente, são movidos para a fila de mensagens não entregues uma vez a contagem máxima de entrega foi excedida.

Se precisa de um alto grau de confiabilidade para processamento de mensagens e processamento leva um trabalho significativo e a hora, recomenda-se que utilize a funcionalidade de obtenção prévia moderadamente ou nada.

Se precisar de débito elevado e processamento de mensagens é normalmente barato, obtenção prévia produz benefícios significativos de débito.

A contagem de obtenção prévia do máximo e a duração do bloqueio configurado na fila ou subscrição tem de ser balanceado, de modo a que o tempo limite de bloqueio, pelo menos, excede a mensagem esperada cumulativa tempo para o tamanho máximo da memória intermédia de obtenção prévia de processamento, além de uma mensagem. Ao mesmo tempo, o tempo limite de bloqueio deve não deve ser, desde que as mensagens podem exceder máximas [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) quando acidentalmente são ignorados, exigindo assim seu bloqueio expirar antes de a ser reenviada.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
