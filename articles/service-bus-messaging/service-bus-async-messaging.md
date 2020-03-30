---
title: Service Bus mensagens assíncronas / Microsoft Docs
description: Saiba como o Azure Service Bus suporta o assincronismo através de uma loja e mecanismo para a frente com filas, tópicos e subscrições.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761037"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronos e alta disponibilidade

As mensagens assíncronas podem ser implementadas de várias maneiras diferentes. Com filas, tópicos e subscrições, o Azure Service Bus suporta o assincronismo através de uma loja e mecanismo de encaminhamento. No funcionamento normal (sincronizado), envia mensagens para filas e tópicos e recebe mensagens de filas e subscrições. As aplicações que escreve dependem destas entidades estarem sempre disponíveis. Quando a entidade muda de saúde, devido a uma variedade de circunstâncias, você precisa de uma maneira de fornecer uma entidade de capacidade reduzida que possa satisfazer a maioria das necessidades.

As aplicações normalmente usam padrões de mensagens assíncronos para permitir uma série de cenários de comunicação. Pode construir aplicações nas quais os clientes podem enviar mensagens para serviços, mesmo quando o serviço não está em funcionamento. Para aplicações que experimentam explosões de comunicações, uma fila pode ajudar a nivelar a carga fornecendo um lugar para comunicações tampão. Finalmente, pode obter um simples mas eficaz equilibrador de carga para distribuir mensagens em várias máquinas.

Para manter a disponibilidade de qualquer uma destas entidades, considere uma série de formas diferentes de estas entidades poderem aparecer indisponíveis para um sistema de mensagens duráveis. De um modo geral, vemos a entidade ficar indisponível para aplicações que escrevemos das seguintes formas diferentes:

* Incapaz de enviar mensagens.
* Incapaz de receber mensagens.
* Incapaz escê-lo de gerir entidades (criar, recuperar, atualizar ou excluir entidades).
* Incapaz de contactar o serviço.

Para cada uma destas falhas, existem diferentes modos de falha que permitem que uma aplicação continue a realizar trabalhos a um certo nível de capacidade reduzida. Por exemplo, um sistema que pode enviar mensagens mas não as receber ainda pode receber encomendas de clientes, mas não pode processar essas encomendas. Este tópico discute potenciais questões que podem ocorrer e como essas questões são atenuadas. A Service Bus introduziu uma série de atenuações nas quais deve optar, e este tópico também discute as regras que regem a utilização dessas mitigações de opt-in.

## <a name="reliability-in-service-bus"></a>Fiabilidade no ônibus de serviço
Existem várias formas de lidar com as questões da mensagem e da entidade, e existem orientações que regem a utilização adequada dessas mitigações. Para compreender as diretrizes, primeiro deve entender o que pode falhar no Service Bus. Devido ao design dos sistemas Azure, todas estas questões tendem a ser de curta duração. A um nível elevado, as diferentes causas de indisponibilidade aparecem da seguinte forma:

* Estrangulando de um sistema externo de que depende o Bus de Serviço. O estrangulamento ocorre a partir de interações com recursos de armazenamento e computação.
* Emissão para um sistema de que depende o Service Bus. Por exemplo, uma determinada parte do armazenamento pode encontrar problemas.
* Falha do Ônibus de serviço em subsistema único. Nesta situação, um nó computacional pode entrar num estado inconsistente e deve reiniciar-se, fazendo com que todas as entidades que serve para carregar o equilíbrio para outros nós. Isto, por sua vez, pode causar um curto período de processamento lento de mensagens.
* Falha do Ônibus de Serviço dentro de um datacenter Azure. Trata-se de uma "falha catastrófica" durante a qual o sistema é inacessível durante muitos minutos ou algumas horas.

> [!NOTE]
> O termo **armazenamento** pode significar tanto o Armazenamento Azure como o SQL Azure.
> 
> 

A Service Bus contém uma série de atenuações para estas questões. As seguintes secções discutem cada questão e respetivas atenuações.

### <a name="throttling"></a>Limitação
Com o Service Bus, o estrangulamento permite a gestão da taxa de mensagens cooperativas. Cada nó de ônibus de serviço individual abriga muitas entidades. Cada uma dessas entidades faz exigências sobre o sistema em termos de CPU, memória, armazenamento e outras facetas. Quando qualquer uma destas facetas deteta o uso que excede os limiares definidos, o Service Bus pode negar um determinado pedido. O chamador recebe uma [ServerBusyException][ServerBusyException] e tenta novamente após 10 segundos.

Como atenuação, o código deve ler o erro e parar quaisquer repetições da mensagem durante pelo menos 10 segundos. Uma vez que o erro pode acontecer em todas as peças da aplicação do cliente, espera-se que cada peça execute a lógica de retry de forma independente. O código pode reduzir a probabilidade de ser estrangulado permitindo a divisão numa fila ou tópico.

### <a name="issue-for-an-azure-dependency"></a>Questão para uma dependência azure
Outros componentes dentro do Azure podem ocasionalmente ter problemas de serviço. Por exemplo, quando um sistema que o Service Bus utiliza está a ser atualizado, esse sistema pode experimentar temporariamente capacidades reduzidas. Para contornar este tipo de problemas, a Service Bus investiga e implementa regularmente mitigações. Os efeitos colaterais destas mitigações aparecem. Por exemplo, para lidar com problemas transitórios com armazenamento, o Service Bus implementa um sistema que permite que as operações de envio de mensagens funcionem de forma consistente. Devido à natureza da mitigação, uma mensagem enviada pode demorar até 15 minutos a aparecer na fila ou subscrição afetadae estar pronta para uma operação de receção. De um modo geral, a maioria das entidades não vai experimentar esta questão. No entanto, dado o número de entidades em Service Bus dentro do Azure, esta mitigação é por vezes necessária para um pequeno subconjunto de clientes de Ônibus de serviço.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha de ônibus de serviço em um único subsistema
Com qualquer aplicação, as circunstâncias podem fazer com que um componente interno do Service Bus se torne inconsistente. Quando a Service Bus o deteta, recolhe dados do pedido de ajuda no diagnóstico do que aconteceu. Uma vez recolhidos os dados, a aplicação é reiniciada numa tentativa de devolvê-lo a um estado consistente. Este processo acontece bastante rapidamente, e resulta em uma entidade que parece estar indisponível por até alguns minutos, embora os tempos de descida típicos sejam muito mais curtos.

Nestes casos, a aplicação do cliente gera uma exceção [system.TimeoutException][System.TimeoutException] ou [MessagingException.][MessagingException] O Service Bus contém uma mitigação para este problema sob a forma de lógica automatizada de retry do cliente. Uma vez esgotado o período de retry e a mensagem não for entregue, pode explorar usando outros mencionados no artigo sobre o tratamento de [interrupções e desastres.][handling outages and disasters]

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico das mensagens assíncronas no Service Bus, leia mais detalhes sobre o tratamento de [interrupções e desastres.][handling outages and disasters]

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
