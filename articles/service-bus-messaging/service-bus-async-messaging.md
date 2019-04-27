---
title: Mensagens assíncronas do Service Bus | Documentos da Microsoft
description: Descrição de mensagens assíncronas do Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531120"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronas e elevada disponibilidade

Mensagens assíncronas podem ser implementadas numa variedade de formas diferentes. Com filas, tópicos e subscrições, o Azure Service Bus suporta asynchronism através de um arquivo e o mecanismo de encaminhamento. Na operação de normal (síncrona), enviar mensagens para filas e tópicos e receber mensagens de filas e subscrições. Aplicativos que escreve dependem estas entidades sempre estejam disponíveis. Quando o estado de funcionamento da entidade é alterado, devido a uma variedade de circunstâncias, terá de uma maneira de fornecer uma entidade de capacidade reduzida que pode satisfazer a maioria das necessidades.

Aplicativos normalmente usam padrões de mensagens assíncronas para permitir que um número de cenários de comunicação. Pode criar aplicações em que os clientes podem enviar mensagens a serviços, mesmo quando o serviço não está em execução. Para aplicativos essa experiência rajadas de comunicações, uma fila podem ajudar a redistribuição de carga, fornecendo um lugar para comunicações de memória intermédia. Por fim, pode obter um balanceador de carga simples, mas eficaz para distribuir as mensagens em várias máquinas.

Para manter a disponibilidade de qualquer uma destas entidades, considere uma série de diferentes formas em que estas entidades podem aparecer indisponíveis para um sistema de mensagens durável. Em termos gerais, vemos a entidade se tornar disponível para aplicativos que escrevemos das seguintes formas diferentes:

* Não é possível enviar mensagens.
* Não é possível receber mensagens.
* Não é possível gerir as entidades (criar, obter, atualizar ou eliminar entidades).
* Não é possível contactar o serviço.

Para cada uma destas falhas, modos de falha diferentes existem que permitem que uma aplicação continuar a realizar o trabalho em algum nível de funcionalidade reduzida. Por exemplo, um sistema que pode enviar mensagens, mas não recebê-las pode receber pedidos de clientes, mas não é possível processar esses pedidos. Este tópico descreve problemas potenciais que podem ocorrer e como esses problemas são atenuados. Do Service Bus tem introduziu uma série de atenuações que deve optar, e este tópico também explica as regras que regem a utilização dessas atenuações participar.

## <a name="reliability-in-service-bus"></a>Confiabilidade no Service Bus
Existem várias formas de lidar com problemas de mensagem e das entidades e existem diretrizes que regem a utilização apropriada dessas atenuações. Para compreender as diretrizes, deve primeiro compreender o que pode falhar no Service Bus. Devido ao design de sistemas do Azure, todos esses problemas tendem a ser curta duração. Num alto nível, as causas diferentes de indisponibilidade aparecem da seguinte maneira:

* Limitação de um sistema externo de que depende do Service Bus. Limitação ocorre de interações com os recursos de computação e armazenamento.
* Problema para um sistema de que depende do Service Bus. Por exemplo, uma parte determinada de armazenamento pode sofrer problemas.
* Falha do Service Bus no subsistema único. Nesta situação, um nó de computação pode entrar num estado inconsistente e tem de reiniciar em si, fazendo com que todas as entidades que ele serve para equilibrar a outros nós. Isso por sua vez, pode causar um curto período de tempo de processamento de mensagens lenta.
* Falha do Service Bus num datacenter do Azure. Esta é uma "Falha catastrófica" durante o qual o sistema estiver inacessível por número de minutos ou algumas horas.

> [!NOTE]
> O termo **armazenamento** pode significar o armazenamento do Azure e SQL Azure.
> 
> 

Barramento de serviço contém um número de atenuações para esses problemas. As secções seguintes abordam cada problema e seus respectivas atenuações.

### <a name="throttling"></a>Limitação
Com o Service Bus, a limitação permite a gestão de taxa de mensagem cooperativo. Cada nó do Service Bus individual hospeda muitas entidades. Cada uma dessas entidades exige o sistema em termos de CPU, memória, armazenamento e outras facetas. Quando qualquer um dessas facetas Deteta utilização exceder os limites definidos, do Service Bus pode negar uma determinada solicitação. O autor da chamada recebe uma [ServerBusyException] [ ServerBusyException] e repetições após 10 segundos.

Facilitar a, o código tem do erro de leitura e de parar qualquer repetições da mensagem por, pelo menos, 10 segundos. Uma vez que o erro pode acontecer em partes do aplicativo cliente, espera-se que cada parte independentemente executa a lógica de repetição. O código pode reduzir a probabilidade de limitação, permitindo a criação de partições numa fila ou tópico.

### <a name="issue-for-an-azure-dependency"></a>Problema de uma dependência do Azure
Outros componentes no Azure, ocasionalmente, podem ter problemas de serviço. Por exemplo, quando um sistema que utiliza o Service Bus está a ser atualizado, esse sistema temporariamente pode conhecer recursos reduzidos. Para contornar esses tipos de problemas, o Service Bus regularmente investiga e implementa atenuações. Efeitos colaterais dessas atenuações aparecer. Por exemplo, para lidar com problemas transitórios com o armazenamento, o Service Bus implementa um sistema que permite operações de envio de mensagem trabalhar de forma consistente. Devido à natureza da atenuação, uma mensagem enviada pode demorar até 15 minutos a aparecer na fila afetada ou subscrição e estarão prontos para uma operação de receção. Em termos gerais, a maior parte das entidades não irá ocorrer este problema. No entanto, dado o número de entidades no Service Bus no Azure, esta redução, às vezes, é necessário para um pequeno subconjunto de clientes do Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha do Service Bus num subsistema único
Com qualquer aplicativo, circunstâncias podem fazer com que um componente interno do Service Bus para se tornar inconsistente. Quando o Service Bus detete isso, recolhe os dados da aplicação para ajudar a diagnosticar o que aconteceu. Quando os dados são recolhidos, o aplicativo for reiniciado numa tentativa para devolvê-lo para um estado consistente. Este processo ocorre rapidamente, e os resultados numa entidade de parecerem estar a ser indisponível para até alguns minutos, embora típicos redução são muito menores.

Nestes casos, a aplicação cliente gera um [System.TimeoutException] [ System.TimeoutException] ou [MessagingException] [ MessagingException] exceção. Barramento de serviço contém uma atenuação para este problema na forma de lógica de repetição de clientes automatizada. Depois do período de repetição se esgota, e não é possível entregar a mensagem, pode explorar usando outro mencionado no artigo na [lidar com interrupções e desastres][handling outages and disasters].

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de mensagens assíncronas no Service Bus, leia mais detalhes sobre [lidar com interrupções e desastres][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
