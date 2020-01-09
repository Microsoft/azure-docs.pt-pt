---
title: Descrição Geral dos Reliable Actors do Service Fabric
description: Introdução ao modelo de programação de Reliable Actors Service Fabric, com base no padrão de ator virtual.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645570"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introdução aos Reliable Actors do Service Fabric
Reliable Actors é uma estrutura de aplicativo Service Fabric com base no padrão de [ator virtual](https://research.microsoft.com/en-us/projects/orleans/) . A API de Reliable Actors fornece um modelo de programação de thread único criado com base nas garantias de escalabilidade e confiabilidade fornecidas pelo Service Fabric.

## <a name="what-are-actors"></a>O que são atores?
Um ator é uma unidade isolada e independente de computação e estado com execução de thread único. O [padrão de ator](https://en.wikipedia.org/wiki/Actor_model) é um modelo computacional para sistemas simultâneos ou distribuídos em que um grande número desses atores pode ser executado simultaneamente e independentemente um do outro. Os atores podem se comunicar entre si e podem criar mais atores.

### <a name="when-to-use-reliable-actors"></a>Quando usar Reliable Actors
Service Fabric Reliable Actors é uma implementação do padrão de design de ator. Assim como ocorre com qualquer padrão de design de software, a decisão de usar um padrão específico é feita com base em se um problema de design de software se ajusta ou não ao padrão.

Embora o padrão de design de ator possa ser uma boa opção para vários cenários e problemas de sistemas distribuídos, é necessário considerar atentamente as restrições do padrão e a estrutura que a implementa. Como orientação geral, considere o padrão de ator para modelar seu problema ou cenário se:

* O espaço do problema envolve um grande número (milhares ou mais) de unidades pequenas, independentes e isoladas de estado e lógica.
* Você deseja trabalhar com objetos de thread único que não exigem uma interação significativa de componentes externos, incluindo a consulta de estado em um conjunto de atores.
* Suas instâncias de ator não bloqueiam os chamadores com atrasos imprevisíveis emitindo operações de e/s.

## <a name="actors-in-service-fabric"></a>Atores no Service Fabric
No Service Fabric, os atores são implementados na estrutura de Reliable Actors: uma estrutura de aplicativo baseada em padrão de ator criada com base em [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Cada serviço de ator confiável que você escreve é, na verdade, um serviço confiável particionado e com estado.

Cada ator é definido como uma instância de um tipo de ator, idêntico ao modo como um objeto .NET é uma instância de um tipo .NET. Por exemplo, pode haver um tipo de ator que implementa a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada ator desse tipo é identificado exclusivamente por uma ID de ator.

## <a name="actor-lifetime"></a>Tempo de vida do ator
Os atores de Service Fabric são virtuais, o que significa que seu tempo de vida não está vinculado à sua representação na memória. Como resultado, eles não precisam ser criados ou destruídos explicitamente. O tempo de execução de Reliable Actors ativa automaticamente um ator na primeira vez que recebe uma solicitação para essa ID de ator. Se um ator não for usado por um período de tempo, a Reliable Actors lixo de tempo de execução coleta o objeto na memória. Ele também manterá o conhecimento da existência do ator, caso ele precise ser reativado mais tarde. Para obter mais detalhes, consulte [ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md).

Essa abstração do tempo de vida do ator virtual traz algumas advertências como resultado do modelo de ator virtual e, de fato, a Reliable Actors implementação se desvia às vezes desse modelo.

* Um ator é ativado automaticamente (fazendo com que um objeto de ator seja construído) na primeira vez que uma mensagem é enviada para sua ID de ator. Após um período de tempo, o objeto de ator é coletado pelo lixo. No futuro, usando a ID de ator novamente, faz com que um novo objeto de ator seja construído. O estado de um ator é o tempo de vida do objeto quando armazenado no Gerenciador de estado.
* Chamar qualquer método de ator para uma ID de ator ativa esse ator. Por esse motivo, os tipos de ator têm seu construtor chamado implicitamente pelo tempo de execução. Portanto, o código do cliente não pode passar parâmetros para o construtor do tipo de ator, embora os parâmetros possam ser passados para o construtor do ator pelo próprio serviço. O resultado é que os atores podem ser construídos em um estado parcialmente inicializado no momento em que outros métodos são chamados nele, se o ator exigir parâmetros de inicialização do cliente. Não há um ponto de entrada único para a ativação de um ator do cliente.
* Embora Reliable Actors Crie objetos de ator implicitamente; Você tem a capacidade de excluir explicitamente um ator e seu estado.

## <a name="distribution-and-failover"></a>Distribuição e failover
Para fornecer escalabilidade e confiabilidade, o Service Fabric distribui atores em todo o cluster e os migra automaticamente de nós com falha para os íntegros, conforme necessário. Essa é uma abstração em um [serviço de confiança particionado e com estado](service-fabric-concepts-partitioning.md). A distribuição, escalabilidade, confiabilidade e failover automático são fornecidos em virtude do fato de que os atores estão sendo executados dentro de um serviço confiável com estado chamado de *serviço de ator*.

Os atores são distribuídos entre as partições do serviço de ator e essas partições são distribuídas entre os nós em um Cluster Service Fabric. Cada partição de serviço contém um conjunto de atores. O Service Fabric gerencia a distribuição e o failover das partições de serviço.

Por exemplo, um serviço de ator com nove partições implantadas em três nós usando o posicionamento da partição de ator padrão seria distribuído de forma prodefinida:

![Distribuição de Reliable Actors][2]

A estrutura de ator gerencia o esquema de partição e as configurações de intervalo de chaves para você. Isso simplifica algumas opções, mas também apresenta uma consideração:

* Reliable Services permite que você escolha um esquema de particionamento, um intervalo de chaves (ao usar um esquema de particionamento de intervalo) e a contagem de partições. Reliable Actors é restrito ao esquema de particionamento de intervalo (o esquema Int64 uniforme) e requer que você use o intervalo de chaves Int64 completo.
* Por padrão, os atores são colocados aleatoriamente em partições resultando em distribuição uniforme.
* Como os atores são colocados aleatoriamente, deve-se esperar que as operações de ator sempre exijam a comunicação de rede, incluindo serialização e desserialização de dados de chamada de método, incorrer em latência e sobrecarga.
* Em cenários avançados, é possível controlar o posicionamento da partição de ator usando IDs de ator Int64 que mapeiam para partições específicas. No entanto, isso pode resultar em uma distribuição desbalanceada de atores entre partições.

Para obter mais informações sobre como os serviços de ator são particionados, consulte [conceitos de particionamento para atores](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Comunicação de ator
As interações de ator são definidas em uma interface que é compartilhada pelo ator que implementa a interface e o cliente que obtém um proxy para um ator por meio da mesma interface. Como essa interface é usada para invocar métodos de ator de forma assíncrona, todos os métodos na interface devem ser retornados por tarefa.

As invocações de método e suas respostas eventualmente resultam em solicitações de rede em todo o cluster, portanto, os argumentos e os tipos de resultado das tarefas que eles retornam devem ser serializáveis pela plataforma. Em particular, eles devem ser [serializáveis de contrato de dados](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>O proxy de ator
A API de cliente do Reliable Actors fornece comunicação entre uma instância de ator e um cliente de ator. Para se comunicar com um ator, um cliente cria um objeto proxy de ator que implementa a interface do ator. O cliente interage com o ator invocando métodos no objeto proxy. O proxy de ator pode ser usado para comunicação de cliente para ator e de ator para ator.

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


Observe que as duas informações usadas para criar o objeto proxy de ator são a ID do ator e o nome do aplicativo. A ID de ator identifica exclusivamente o ator, enquanto o nome do aplicativo identifica o [Service Fabric aplicativo](service-fabric-reliable-actors-platform.md#application-model) em que o ator é implantado.

A classe `ActorProxy`C#()/`ActorProxyBase`(Java) no lado do cliente executa a resolução necessária para localizar o ator por ID e abrir um canal de comunicação com ele. Ele também tenta localizar o ator novamente nos casos de falhas de comunicação e failovers. Como resultado, a entrega de mensagens tem as seguintes características:

* A entrega de mensagens é o melhor esforço.
* Os atores podem receber mensagens duplicadas do mesmo cliente.

## <a name="concurrency"></a>Simultaneidade
O tempo de execução de Reliable Actors fornece um modelo simples de acesso baseado em opção para acessar métodos de ator. Isso significa que não mais de um thread pode estar ativo dentro do código de um objeto de ator a qualquer momento. O acesso baseado em troca simplifica muito os sistemas simultâneos, pois não há necessidade de mecanismos de sincronização para o acesso a dados. Isso também significa que os sistemas devem ser projetados com considerações especiais para a natureza de acesso de thread único de cada instância de ator.

* Uma única instância de ator não pode processar mais de uma solicitação por vez. Uma instância de ator pode causar um afunilamento de taxa de transferência se for esperado manipular solicitações simultâneas.
* Os atores podem deadlock entre si se houver uma solicitação circular entre dois atores, enquanto uma solicitação externa é feita para um dos atores simultaneamente. O tempo de execução do ator atingirá automaticamente o limite de chamadas de ator e lançará uma exceção para o chamador para interromper possíveis situações de deadlock.

![Reliable Actors comunicação][3]

### <a name="turn-based-access"></a>Acesso baseado em ativação
Uma vez consiste na execução completa de um método de ator em resposta a uma solicitação de outros atores ou clientes, ou a execução completa de um retorno de chamada de [timer/lembrete](service-fabric-reliable-actors-timers-reminders.md) . Embora esses métodos e retornos de chamada sejam assíncronos, o tempo de execução dos atores não os intercala. Uma rodada deve ser totalmente concluída antes que uma nova ativação seja permitida. Em outras palavras, um método de ator ou um retorno de chamada de timer/lembrete que está em execução no momento deve ser totalmente concluído antes que uma nova chamada para um método ou retorno de chamada seja permitida. Um método ou retorno de chamada é considerado como concluído se a execução foi retornada do método ou retorno de chamada e a tarefa retornada pelo método ou retorno de chamada foi concluída. Vale a pena enfatizar que a simultaneidade baseada em desligamento é respeitada mesmo entre diferentes métodos, temporizadores e retornos de chamada.

O tempo de execução dos atores impõe a simultaneidade baseada em ativação adquirindo um bloqueio por ator no início de uma rodada e liberando o bloqueio no final da rodada. Portanto, a simultaneidade baseada em troca é imposta por ator e não entre atores. Os métodos de ator e retornos de chamada de timer/lembrete podem ser executados simultaneamente em nome de atores diferentes.

O exemplo a seguir ilustra os conceitos acima. Considere um tipo de ator que implementa dois métodos assíncronos (digamos, *Method1* e *Method2*), um temporizador e um lembrete. O diagrama a seguir mostra um exemplo de uma linha do tempo para a execução desses métodos e retornos de chamada em nome de dois atores (*ActorId1* e *ActorId2*) que pertencem a esse tipo de ator.

![Simultaneidade e acesso com base em Reliable Actors em tempo de execução][1]

Este diagrama segue estas convenções:

* Cada linha vertical mostra o fluxo lógico de execução de um método ou um retorno de chamada em nome de um ator específico.
* Os eventos marcados em cada linha vertical ocorrem em ordem cronológica, com eventos mais recentes que ocorrem abaixo dos mais antigos.
* Cores diferentes são usadas para linhas do tempo correspondentes a atores diferentes.
* Realce é usado para indicar a duração para a qual o bloqueio por ator é mantido em nome de um método ou retorno de chamada.

Alguns pontos importantes a considerar:

* Enquanto *Method1* está sendo executado em nome de *ActorId2* em resposta à solicitação do cliente *xyz789*, outra solicitação de cliente (*Abc123*) chega que também requer que *Method1* seja executado por *ActorId2*. No entanto, a segunda execução de *Method1* não começa até que a execução anterior seja concluída. Da mesma forma, um lembrete registrado por *ActorId2* é acionado enquanto *Method1* está sendo executado em resposta à solicitação do cliente *xyz789*. O retorno de chamada de lembrete é executado somente depois que ambas as execuções de *Method1* são concluídas. Tudo isso se deve à imposição da simultaneidade baseada em ativação para *ActorId2*.
* Da mesma forma, a simultaneidade baseada em troca também é imposta para *ActorId1*, conforme demonstrado pela execução de *Method1*, *Method2*e o retorno de chamada do temporizador em nome de *ActorId1* acontecendo de maneira serial.
* A execução de *Method1* em nome de *ActorId1* se sobrepõe à sua execução em nome de *ActorId2*. Isso ocorre porque a simultaneidade baseada em troca é imposta somente dentro de um ator e não entre atores.
* Em algumas das execuções de método/retorno de chamada, oC#`Task`()/`CompletableFuture`(Java) retornado pelo método/retorno de chamada termina após o retorno do método. Em alguns outros, a operação assíncrona já foi concluída no momento em que o método/retorno de chamada retorna. Em ambos os casos, o bloqueio por ator é liberado somente depois que o método/retorno de chamada retorna e a operação assíncrona é concluída.

### <a name="reentrancy"></a>Reentrada
O tempo de execução dos atores permite a reentrância por padrão. Isso significa que, se um método de ator de *ator a* chamar um método no *ator B*, que, por sua vez, chamará outro método no *ator A*, esse método poderá ser executado. Isso ocorre porque ele faz parte do mesmo contexto lógico da cadeia de chamadas. Todas as chamadas de timer e lembrete começam com o novo contexto de chamada lógica. Consulte a [reentrada Reliable Actors](service-fabric-reliable-actors-reentrancy.md) para obter mais detalhes.

### <a name="scope-of-concurrency-guarantees"></a>Escopo de garantias de simultaneidade
O tempo de execução dos atores fornece essas garantias de simultaneidade em situações em que ele controla a invocação desses métodos. Por exemplo, ele fornece essas garantias para as invocações de método que são feitas em resposta a uma solicitação de cliente, bem como para retornos de chamada de timer e lembrete. No entanto, se o código do ator invocar diretamente esses métodos fora dos mecanismos fornecidos pelo tempo de execução dos atores, o tempo de execução não poderá fornecer nenhuma garantia de simultaneidade. Por exemplo, se o método for invocado no contexto de alguma tarefa que não está associada à tarefa retornada pelos métodos de ator, o tempo de execução não poderá fornecer garantias de simultaneidade. Se o método for invocado de um thread que o ator cria por conta própria, o tempo de execução também não poderá fornecer garantias de simultaneidade. Portanto, para executar operações em segundo plano, os atores devem usar [temporizadores de ator e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md) que respeitam a simultaneidade baseada em folheio.

## <a name="next-steps"></a>Passos seguintes
Comece criando seu primeiro serviço de Reliable Actors:
   * [Introdução ao Reliable Actors no .NET](service-fabric-reliable-actors-get-started.md)
   * [Introdução ao Reliable Actors em Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
