---
title: Descrição geral dos Reliable Actors de recursos de infraestrutura do serviço | Documentos da Microsoft
description: Introdução ao modelo de programação dos Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 5a237e23dffed76e6122e17b59c85d20ca7e1baf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668675"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introdução aos Reliable Actors do Service Fabric
Reliable Actors é uma estrutura de aplicação do Service Fabric com base na [Ator Virtual](https://research.microsoft.com/en-us/projects/orleans/) padrão. A API de Atores fiáveis fornece um thread único modelo de programação criado sobre as garantias de escalabilidade e confiabilidade fornecidas pelo Service Fabric.

## <a name="what-are-actors"></a>Quais são os Atores?
Um ator é uma unidade de isolado, independente de computação e o estado de execução de thread único. O [padrão de ator](https://en.wikipedia.org/wiki/Actor_model) é um modelo de computacional para sistemas simultâneos ou distribuídos no qual um grande número destes atores pode executar em simultâneo e independentemente uns dos outros. Atores podem comunicar entre si e é possível criar mais de atores.

### <a name="when-to-use-reliable-actors"></a>Quando utilizar a Reliable Actors
Reliable Actors do Service Fabric é uma implementação do padrão de design de ator. Tal como acontece com qualquer padrão de design de software, a decisão se deve utilizar um padrão específico é feita com base no problema de design de um software de se pretende ou não se encaixa o padrão.

Embora o padrão de design de ator pode ser uma boa ajustar a uma série de problemas de sistemas distribuídos e cenários, cuidadosos consideração das restrições do padrão e a estrutura de implementação têm de ser feita. Como orientação geral, considere o padrão de ator para modelar o seu cenário ou um problema se:

* Seu espaço de problema envolve um grande número (milhares ou mais) de pequenos, de forma independentes e isoladas unidades de estado e lógica.
* Pretende trabalhar com objetos de thread único que não exigem significativa interação de componentes externos, incluindo consultas de estado entre um conjunto de atores.
* As instâncias de ator não bloquear chamadores com atrasos imprevisíveis através da emissão de operações de e/s.

## <a name="actors-in-service-fabric"></a>Actors no Service Fabric
No Service Fabric, atores são implementados no framework Reliable Actors: Uma estrutura de aplicações com base padrão de ator embutida no início da [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Cada serviço Reliable Actor que escrever é, na verdade, um particionado e com monitorização de estado Reliable Service.

Cada ator é definido como uma instância de um tipo de ator, idêntica à forma como um objeto .NET é uma instância de um tipo .NET. Por exemplo, pode haver um tipo de ator implementa a funcionalidade de uma calculadora e pode haver vários atores desse tipo que são distribuídas em vários nós num cluster. Cada tal ator é exclusivamente identificado por um ID de ator.

## <a name="actor-lifetime"></a>Tempo de vida de ator
Atores do Service Fabric são virtuais, o que significa que o seu tempo de vida não está ligado à sua representação na memória. Como resultado, não terão de ser explicitamente criado ou destruída. O tempo de execução do Reliable Actors ativa automaticamente uma hora de ator o primeiro que recebe um pedido para esse ID de ator. Se um ator não for utilizado durante um período de tempo, o tempo de execução do Reliable Actors lixo-recolhe o objeto na memória. Também mantém dados de conhecimento da existência do ator ele precisa ser reativado mais tarde. Para obter mais detalhes, consulte [coleção de ciclo de vida e libertação da memória de Ator](service-fabric-reliable-actors-lifecycle.md).

Essa abstração de tempo de vida de ator virtual apresenta algumas limitações, como resultado do modelo de ator virtual e, na verdade a implementação de Reliable Actors desvia às vezes deste modelo.

* Um ator é ativado automaticamente (fazendo com que um objeto de ator a construção) na primeira vez que é enviada uma mensagem ao seu ID de ator. Após um determinado período de tempo, o objeto de ator é coletado pelo lixo. No futuro, com o ID de actor novamente, faz com que um novo objeto de ator a construção. Estado de um ator outlives tempo de vida do objeto quando armazenada no Gestor de estado.
* Chamar qualquer método de ator um ID de actor ativa essa ator. Por esse motivo, os tipos de ator tem seu construtor chamado implicitamente pelo tempo de execução. Por conseguinte, código de cliente não é possível transmitir parâmetros ao construtor do tipo de ator, embora parâmetros podem ser passados ao construtor do ator pelo próprio serviço. O resultado é que os atores podem ser construídos num Estado parcialmente inicializado no momento, os outros métodos são chamados se o ator necessitar de parâmetros de inicialização do cliente. Não existe nenhum ponto de entrada única para a ativação de um ator do cliente.
* Embora o Reliable Actors implicitamente criar objetos de ator; Tem a capacidade de excluir explicitamente um ator e seu estado.

## <a name="distribution-and-failover"></a>Distribuição e ativação pós-falha
Para fornecer escalabilidade e confiabilidade, o Service Fabric distribui atores em todo o cluster e migra-os automaticamente de nós com falha para bom estado de funcionamento dos conforme necessário. Esta é uma abstração através de um [Reliable Service com monitorização de estado, particionada](service-fabric-concepts-partitioning.md). Distribuição, a escalabilidade, confiabilidade e a ativação pós-falha automática são todos desde que o fato de que os atores são executados dentro de um serviço com estado fiável chamado os *serviço de Ator*.

Atores são distribuídos pelas partições do serviço de Ator e nessas partições são distribuídas por nós no cluster do Service Fabric. Cada partição de serviço contém um conjunto de atores. O Service Fabric gere e ativação pós-falha das partições do serviço de distribuição.

Por exemplo, um serviço de ator com nove partições implementada em três nós usando o posicionamento de partição de ator padrão teria de ser distribuído tratamento:

![Distribuição do Reliable Actors][2]

A estrutura de Ator faz a gestão de definições de intervalo de esquema e a chave de partição para. Isso simplifica algumas opções, mas também carrega tidos em consideração:

* Reliable Services permite-lhe escolher um esquema de particionamento, o intervalo de chaves (ao utilizar um intervalo de esquema de particionamento) e contagem de partição. Reliable Actors é restrito para o intervalo de esquema (o esquema de Int64 uniforme) de criação de partições e requer que utiliza o intervalo de chave de Int64 completo.
* Por predefinição, os atores aleatoriamente são colocados em partições, resultando em distribuição uniforme.
* Porque atores aleatoriamente são colocados, deve esperar que as operações de ator sempre irão necessitar de comunicação de rede, incluindo a serialização e desserialização de dados de chamada de método, latência e sobrecarga.
* Em cenários avançados, é possível colocação de partição do controle ator utilizando Int64 ator IDs que mapeiam para partições específicas. No entanto, ao fazê-lo por isso, pode resultar numa distribuição desequilibrada de atores em várias partições.

Para obter mais informações sobre como os serviços de atores são particionados, consulte [conceitos de criação de partições para atores](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Comunicação de ator
Interações de ator são definidas numa interface que é partilhada por ator que implementa a interface e o cliente que obtém um proxy para um ator através da mesma interface. Como essa interface é usada para invocar métodos de ator de forma assíncrona, cada método na interface tem de ser que retornam Task.

Invocações de método e suas respostas, por fim, resultam em pedidos de rede no cluster, por isso, os argumentos e os tipos de resultado das tarefas que elas retornam precisam ser serializáveis pela plataforma. Em particular, têm de ser [contrato de dados serializáveis](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>O proxy de ator
A API do cliente de Reliable Actors fornece comunicação entre uma instância de ator e um cliente de ator. Para comunicar com um ator, um cliente cria um objeto de proxy de ator que implementa a interface de ator. O cliente interage com o ator pela invocação de métodos no objeto proxy. O proxy de ator pode ser utilizado para comunicação de cliente para o ator e ator-ator.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Tenha em atenção que os dois conjuntos de informações utilizadas para criar o objeto de proxy de ator são o ID de ator e o nome da aplicação. O ID de actor identifica exclusivamente o ator, enquanto o nome da aplicação identifica os [aplicação do Service Fabric](service-fabric-reliable-actors-platform.md#application-model) em que o ator é implementado.

O `ActorProxy`(C#) / `ActorProxyBase`classe (Java) no lado do cliente efetua a resolução necessária para localizar o ator por ID e abrir um canal de comunicação com o mesmo. Também voltar a tentar para localizar o ator no caso de falhas de comunicação e ativações pós-falha. Como resultado, a entrega de mensagens com as seguintes características:

* Entrega de mensagens é melhor esforço.
* Atores poderão receber mensagens duplicadas a partir do mesmo cliente.

## <a name="concurrency"></a>Simultaneidade
O tempo de execução do Reliable Actors fornece um modelo de acesso baseado em vez de simples para aceder aos métodos de ator. Isso significa que não mais do que um thread pode estar ativo dentro do código de um objeto de ator em qualquer altura. Acesso baseado em vez de simplifica bastante a sistemas em simultâneo como não há necessidade de mecanismos de sincronização para acesso a dados. Isso também significa sistemas têm de ser concebidos com considerações especiais sobre a natureza de acesso de thread único de cada instância de ator.

* Uma instância única de ator não consegue processar mais do que um pedido de cada vez. Uma instância de ator pode causar um gargalo se espera-se para processar pedidos em simultâneo.
* Atores poderá sofrer deadlock entre si se houver um pedido de circular entre dois atores enquanto um pedido externo é feito para um dos atores em simultâneo. O runtime de ator automaticamente o tempo limite em chamadas de ator e lançar uma exceção ao autor da chamada para interromper a situações de deadlock possíveis.

![Comunicação do Reliable Actors][3]

### <a name="turn-based-access"></a>Acesso baseado em vez de
Uma vez consiste na execução completa de um método de ator em resposta a um pedido de outros atores ou clientes ou a execução completa de um [temporizador/lembrete](service-fabric-reliable-actors-timers-reminders.md) retorno de chamada. Embora esses métodos e os retornos de chamada são assíncronos, o tempo de execução de Atores de intercalação não-los. Uma folheada tem de ser totalmente concluída antes de uma nova folheada é permitida. Em outras palavras, um retorno de método ou temporizador/lembrete ator que está sendo executado tem de ser totalmente concluído antes de uma nova chamada para um método ou retorno de chamada é permitido. Um método ou uma chamada de retorno é considerada como terminar se a execução tem retornado do método ou retorno de chamada e o task retornado pelo método ou retorno de chamada foi concluída. Vale a pena enfatizando que a simultaneidade turnos é respeitada mesmo entre diferentes métodos, temporizadores e retornos de chamada.

O tempo de execução de Atores impõe simultaneidade turnos ao adquirir um bloqueio por ator no início de uma vez e liberar o bloqueio no final da ativar. Portanto, com base em vez de simultaneidade é imposta numa base por ator e não em todos os atores. Em nome de atores diferentes, métodos de ator e retornos de chamada do timer/lembrete podem executar em simultâneo.

O exemplo a seguir ilustra os conceitos acima. Considere um tipo de ator implementa dois métodos assíncronos (por exemplo, *Method1* e *Method2*), um temporizador e um lembrete. O diagrama abaixo mostra um exemplo de uma linha do tempo para a execução desses métodos e os retornos de chamada em nome de duas atores (*ActorId1* e *ActorId2*) que pertence a este tipo de ator.

![Simultaneidade de turnos de tempo de execução de Atores fiável e de acesso][1]

Este diagrama segue essas convenções:

* Cada linha vertical mostra o fluxo lógico de execução de um método ou uma chamada de retorno em nome de um ator específico.
* Ocorrerem os eventos marcados em cada linha vertical em ordem cronológica, com novos eventos que ocorrem abaixo os mais antigos.
* Cores diferentes são usadas para linhas cronológicas correspondentes aos atores diferentes.
* Realce é utilizado para indicar a duração para o qual o bloqueio por ator é mantido em nome de um método ou uma chamada de retorno.

Alguns pontos importantes a considerar:

* Embora *Method1* está em execução em nome de *ActorId2* em resposta à solicitação do cliente *xyz789*, outro pedido de cliente (*abc123*) chega de que também requer *Method1* para ser executado por *ActorId2*. No entanto, a segunda execução de *Method1* não começa até que tenha terminado a execução anterior. Da mesma forma, um lembrete registrados pelo *ActorId2* desencadeado ao *Method1* está a ser executado em resposta à solicitação do cliente *xyz789*. O retorno de chamada de lembrete é executado somente depois de ambas as execuções de *Method1* estiverem concluídas. Tudo isso é devido a simultaneidade de turnos imposta na *ActorId2*.
* Da mesma forma, com base em vez de simultaneidade também é imposta para o *ActorId1*, conforme demonstrado pela execução de *Method1*, *Method2*e o retorno de chamada do timer em nome  *ActorId1* acontecendo de forma serial.
* Execução de *Method1* em nome de *ActorId1* sobrepõe-se com sua execução em nome de *ActorId2*. Isso é porque com base em vez de simultaneidade é imposta apenas dentro de um ator e não em todos os atores.
* Em algumas das execuções método/retorno de chamada, o `Task`(C#) / `CompletableFuture`(Java) devolvido pela conclusão do método/retorno de chamada após o método retorna. Em alguns outros, a operação assíncrona já foi concluída no período de que método/retorno de chamada retorna. Em ambos os casos, o bloqueio por ator é lançado apenas depois do método/retorno de chamada retorna e a operação assíncrona for concluída.

### <a name="reentrancy"></a>Reentrada
O tempo de execução de Atores permite reentrância, por predefinição. Isso significa que, se um método de ator de *Ator uma* chama um método na *B de Ator*, que por sua vez, chama outro método no *Ator A*, que o método tem permissão para executar. Isso é porque ele faz parte do mesmo contexto de cadeia de chamadas lógicas. Todas as chamadas de temporizador e lembrete começam com o novo contexto de chamadas lógicas. Consulte a [reentrada dos Reliable Actors](service-fabric-reliable-actors-reentrancy.md) para obter mais detalhes.

### <a name="scope-of-concurrency-guarantees"></a>Âmbito de garantias de simultaneidade
O tempo de execução de Atores fornece essas garantias de simultaneidade em situações em que controla a invocação dos seguintes métodos. Por exemplo, ele fornece essas garantias para as invocações de método que são realizadas em resposta a um pedido de cliente, bem como para retornos de chamada do timer e lembrete. No entanto, se o código de ator diretamente invoca esses métodos fora dos mecanismos fornecidos pelo tempo de execução de Atores, o tempo de execução não fornece quaisquer garantias de simultaneidade. Por exemplo, se o método é invocado no contexto de uma tarefa que não esteja associado com o task retornado pelos métodos de ator, em seguida, o tempo de execução não é possível fornecer garantias de simultaneidade. Se o método é invocado a partir de um thread que o ator cria por si, em seguida, o tempo de execução também não é possível fornecer garantias de simultaneidade. Portanto, para efetuar operações em segundo plano, atores devem usar [temporizadores de ator e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md) que respeitamos turnos de simultaneidade.

## <a name="next-steps"></a>Passos Seguintes
Comece criando seu primeiro serviço do Reliable Actors:
   * [Introdução ao Reliable Actors no .NET](service-fabric-reliable-actors-get-started.md)
   * [Introdução ao Reliable Actors em Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
