---
title: Mensagens assíncronias do Service Bus Microsoft Docs
description: Saiba como a Azure Service Bus suporta o assíncronismo através de uma loja e mecanismo de avanço com filas, tópicos e subscrições.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 0ea2a2f51d29f1a65926509581df49b453bde59f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067550"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronos e alta disponibilidade

As mensagens assíncronos podem ser implementadas de várias maneiras. Com filas, tópicos e subscrições, a Azure Service Bus suporta o assíncrona através de uma loja e mecanismo de avanço. No funcionamento normal (sincronizado), envia mensagens para filas e tópicos e recebe mensagens de filas e subscrições. As candidaturas que escreve dependem de estas entidades estarem sempre disponíveis. Quando a entidade muda de saúde, devido a uma variedade de circunstâncias, você precisa de uma forma de fornecer uma entidade de capacidade reduzida que possa satisfazer a maioria das necessidades.

As aplicações normalmente usam padrões de mensagens assíncronos para permitir uma série de cenários de comunicação. Pode construir aplicações nas quais os clientes podem enviar mensagens para os serviços, mesmo quando o serviço não está a funcionar. Para aplicações que experimentam explosões de comunicações, uma fila pode ajudar a nivelar a carga fornecendo um lugar para comunicações tampão. Finalmente, pode obter um equilibrador de carga simples mas eficaz para distribuir mensagens em várias máquinas.

De forma a manter a disponibilidade de qualquer uma destas entidades, considere uma série de formas diferentes de estas entidades poderem parecer indisponíveis para um sistema de mensagens duráveis. De um modo geral, vemos a entidade ficar indisponível para aplicações que escrevemos de diferentes formas:

* Incapaz de enviar mensagens.
* Incapaz de receber mensagens.
* Não é possível gerir entidades (criar, recuperar, atualizar ou eliminar entidades).
* Não é possível contactar o serviço.

Para cada uma destas falhas existem diferentes modos de falha que permitem que uma aplicação continue a realizar trabalhos a um certo nível de capacidade reduzida. Por exemplo, um sistema que possa enviar mensagens mas não as receber ainda pode receber encomendas de clientes, mas não pode processar essas encomendas. Este tópico discute potenciais questões que podem ocorrer e como essas questões são atenuadas. A Service Bus introduziu uma série de mitigações que deve optar, e este tópico também discute as regras que regem o uso dessas mitigações de opt-in.

## <a name="reliability-in-service-bus"></a>Fiabilidade no ônibus de serviço
Existem várias formas de lidar com as questões da mensagem e das entidades, e existem orientações que regem o uso adequado dessas mitigações. Para compreender as diretrizes, tem primeiro de compreender o que pode falhar no Service Bus. Devido ao design dos sistemas Azure, todas estas questões tendem a ser de curta duração. A um nível elevado, as diferentes causas de indisponibilidade aparecem da seguinte forma:

* Aceleração de um sistema externo de que depende o Service Bus. O estrangulamento ocorre a partir de interações com recursos de armazenamento e computação.
* Problema para um sistema de que depende o Service Bus. Por exemplo, uma determinada parte do armazenamento pode encontrar problemas.
* Falha do Service Bus no subsistema único. Nesta situação, um nó computacional pode entrar num estado inconsistente e deve reiniciar-se, fazendo com que todas as entidades que serve para carregar o equilíbrio a outros nós. Isto, por sua vez, pode causar um curto período de processamento de mensagens lentas.
* Falha do Service Bus dentro de um datacenter Azure. Trata-se de uma "falha catastrófica" durante a qual o sistema é inacessível por muitos minutos ou algumas horas.

> [!NOTE]
> O termo **armazenamento** pode significar tanto o Azure Storage como o SQL Azure.
> 
> 

O Service Bus contém uma série de mitigações para estas questões. As seguintes secções discutem cada edição e respetivas atenuações.

### <a name="throttling"></a>Limitação
Com a Service Bus, o estrangulamento permite a gestão da taxa de mensagem cooperativa. Cada nó de ônibus de serviço individual abriga muitas entidades. Cada uma dessas entidades faz exigências ao sistema em termos de CPU, memória, armazenamento e outras facetas. Quando qualquer uma destas facetas deteta uma utilização que exceda os limiares definidos, o Service Bus pode negar um determinado pedido. O chamador recebe um [ServerBusyException][ServerBusyException] e retréis após 10 segundos.

Como atenuação, o código deve ler o erro e parar qualquer retrete da mensagem durante pelo menos 10 segundos. Uma vez que o erro pode acontecer em todas as peças da aplicação do cliente, espera-se que cada peça execute a lógica de retrip. O código pode reduzir a probabilidade de ser estrangulado, permitindo a partilha numa fila ou tópico.

### <a name="issue-for-an-azure-dependency"></a>Emissão para uma dependência do Azure
Outros componentes dentro do Azure podem ocasionalmente ter problemas de serviço. Por exemplo, quando um sistema que o Service Bus utiliza está a ser atualizado, esse sistema pode experimentar temporariamente capacidades reduzidas. Para contornar este tipo de questões, a Service Bus investiga e implementa regularmente mitigações. Os efeitos colaterais destas atenuações aparecem. Por exemplo, para lidar com problemas transitórios com armazenamento, o Service Bus implementa um sistema que permite que as operações de envio de mensagens funcionem de forma consistente. Devido à natureza da mitigação, uma mensagem enviada pode demorar até 15 minutos a aparecer na fila ou subscrição afetada e estar pronta para uma operação de receção. De um modo geral, a maioria das entidades não vai experimentar esta questão. No entanto, dado o número de entidades em Service Bus dentro de Azure, esta mitigação é por vezes necessária para um pequeno subconjunto de clientes da Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha do autocarro de serviço num único subsistema
Com qualquer aplicação, as circunstâncias podem fazer com que um componente interno do Service Bus se torne inconsistente. Quando a Service Bus deteta isto, recolhe dados da aplicação para ajudar no diagnóstico do que aconteceu. Uma vez recolhidos os dados, a aplicação é reiniciada numa tentativa de devolvê-lo a um estado consistente. Este processo ocorre bastante rapidamente, e resulta numa entidade que parece não estar disponível por até alguns minutos, embora os tempos típicos de descida sejam muito mais curtos.

Nestes casos, a aplicação do cliente gera uma exceção [System.TimeoutException][System.TimeoutException] ou [MessagingException.][MessagingException] A Service Bus contém uma mitigação para este problema sob a forma de lógica automatizada de relemisão do cliente. Uma vez esgotado o período de retíria e a mensagem não é entregue, pode explorar utilizando outros mencionados no artigo sobre [interrupções e desastres][handling outages and disasters]de manuseamento .

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o básico das mensagens assíncronos na Service Bus, leia mais detalhes sobre [interrupções e desastres.][handling outages and disasters]

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception?view=netcore-3.1
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
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero?view=netcore-3.1
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md