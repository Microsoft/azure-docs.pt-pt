---
title: Visão geral de atores fiáveis de tecido de serviço
description: Introdução ao modelo de programação de Atores Fiáveis de Tecido de Serviço, baseado no padrão de Ator Virtual.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645570"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introdução a atores fiáveis de tecido de serviço
Reliable Actors é uma estrutura de aplicação de tecido de serviço baseada no padrão [de Ator Virtual.](https://research.microsoft.com/en-us/projects/orleans/) A API Reliable Actors fornece um modelo de programação de roscar-se baseado nas garantias de escalabilidade e fiabilidade fornecidas pela Service Fabric.

## <a name="what-are-actors"></a>O que são atores?
Um ator é uma unidade isolada e independente de computação e estado com execução de um fio único. O padrão do [ator](https://en.wikipedia.org/wiki/Actor_model) é um modelo computacional para sistemas simultâneos ou distribuídos em que um grande número destes atores pode executar simultaneamente e independentemente uns dos outros. Os atores podem comunicar uns com os outros e podem criar mais atores.

### <a name="when-to-use-reliable-actors"></a>Quando usar Atores Fiáveis
Service Fabric Reliable Actors é uma implementação do padrão de design de ator. Como em qualquer padrão de design de software, a decisão de usar um padrão específico é tomada com base em se um problema de design de software se encaixa ou não no padrão.

Embora o padrão de design do ator possa ser um bom ajuste para uma série de problemas e cenários de sistemas distribuídos, deve ser feita uma cuidadosa consideração dos constrangimentos do padrão e da estrutura de implementação. Como orientação geral, considere o padrão do ator para modelar o seu problema ou cenário se:

* O seu espaço problemático envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e lógica.
* Você quer trabalhar com objetos de roscar único que não requerem interação significativa de componentes externos, incluindo o estado de consulta através de um conjunto de atores.
* Os seus casos de ator não bloqueiam chamadas com atrasos imprevisíveis ao emitir operações de I/O.

## <a name="actors-in-service-fabric"></a>Atores em Tecido de Serviço
No Tecido de Serviço, os atores são implementados no quadro "Reliable Actors": Um quadro de aplicação baseado em padrões de ator construído em cima de [Serviços Fiáveis](service-fabric-reliable-services-introduction.md)de Tecidos. Cada serviço de Ator Fiável que escreveé na verdade um serviço de confiança dividido e audacionado.

Cada ator é definido como um exemplo de um tipo de ator, idêntico à forma como um objeto .NET é um exemplo de um tipo .NET. Por exemplo, pode haver um tipo de ator que implementa a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós através de um cluster. Cada ator é identificado exclusivamente por uma identificação de ator.

## <a name="actor-lifetime"></a>Vida útil do ator
Os atores de tecido de serviço são virtuais, o que significa que a sua vida não está ligada à sua representação na memória. Como resultado, não precisam de ser explicitamente criados ou destruídos. O tempo de execução dos Atores Fiáveis ativa automaticamente um ator na primeira vez que recebe um pedido para o ID do ator. Se um ator não for utilizado durante um período de tempo, os Atores Fiáveis recolhem o objeto na memória. Também manterá conhecimento da existência do ator caso tenha de ser reativado mais tarde. Para mais detalhes, consulte O Ciclo de [Vida do Ator e a recolha de lixo.](service-fabric-reliable-actors-lifecycle.md)

Esta abstração vitalícia do ator virtual transporta algumas ressalvas como resultado do modelo de ator virtual, e na verdade a implementação de Atores Fiáveis desvia-se por vezes deste modelo.

* Um ator é ativado automaticamente (fazendo com que um objeto ator seja construído) na primeira vez que uma mensagem é enviada para o seu ID ator. Após algum tempo, o objeto do ator é lixo recolhido. No futuro, usando novamente a identificação do ator, faz com que um novo objeto de ator seja construído. O estado de um ator sobrevive à vida do objeto quando está guardado no gerente do estado.
* Chamar qualquer método de ator para uma identificação de ator ativa aquele ator. Por esta razão, os atores têm o seu construtor chamado implicitamente pelo tempo de execução. Portanto, o código do cliente não pode passar parâmetros para o construtor do tipo ator, embora os parâmetros possam ser passados para o construtor do ator pelo próprio serviço. O resultado é que os atores podem ser construídos em um estado parcialmente inicializado no momento em que outros métodos são chamados sobre ele, se o ator necessitar de parâmetros de inicialização do cliente. Não há um único ponto de entrada para a ativação de um ator do cliente.
* Embora os Atores Fiáveis criem implicitamente objetos de ator; tem a capacidade de apagar explicitamente um ator e o seu estado.

## <a name="distribution-and-failover"></a>Distribuição e falha
Para proporcionar escalabilidade e fiabilidade, o Service Fabric distribui atores por todo o cluster e migra-os automaticamente de nós falhados para os saudáveis, conforme necessário. Esta é uma abstração sobre um [serviço de confiança dividido e imponente.](service-fabric-concepts-partitioning.md) A distribuição, escalabilidade, fiabilidade e falha automática são fornecidas em virtude do facto de os atores estarem a correr dentro de um serviço de confiança imponente chamado Serviço de *Ator.*

Os atores são distribuídos pelas divisórias do Serviço de Ator, e essas divisórias são distribuídas pelos nós num cluster de Tecido de Serviço. Cada partição de serviço contém um conjunto de atores. O Service Fabric gere a distribuição e a falha das divisórias de serviço.

Por exemplo, um serviço de ator com nove divisórias implantadas em três nós usando a colocação de partição de ator padrão seria distribuído assim:

![Distribuição de Atores Fiáveis][2]

O Ator Framework gere o esquema de partição e as definições de gama de chaves para si. Isto simplifica algumas escolhas, mas também tem alguma consideração:

* Os Serviços Fiáveis permitem-lhe escolher um esquema de partilha, uma gama de chaves (quando se utiliza um esquema de partilha de gama) e a contagem de divisórias. Os Atores Fiáveis limitam-se ao regime de partilha de gama (o esquema uniforme Int64) e exigem que utilize toda a gama de chaves Int64.
* Por padrão, os atores são colocados aleatoriamente em divisórias, resultando numa distribuição uniforme.
* Como os atores são colocados aleatoriamente, é de esperar que as operações dos atores exijam sempre comunicação em rede, incluindo a serialização e a desserialização dos dados de chamadas de métodos, incorrendo em latência e sobrecarga.
* Em cenários avançados, é possível controlar a colocação da partição de atores usando IDs de ator Int64 que mapeiam para divisórias específicas. No entanto, fazê-lo pode resultar numa distribuição desequilibrada de atores através de divisórias.

Para obter mais informações sobre a forma como os serviços de ator são divididos, consulte conceitos de [divisão para atores.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

## <a name="actor-communication"></a>Comunicação de ator
As interações do ator são definidas numa interface que é partilhada pelo ator que implementa a interface, e o cliente que recebe um representante de um ator através da mesma interface. Como esta interface é usada para invocar métodos de ator assincronicamente, todos os métodos da interface devem ser o retorno da Tarefa.

As invocações de métodos e as suas respostas resultam, em última análise, em pedidos de rede em todo o cluster, pelo que os argumentos e os tipos de resultados das tarefas que devolvem devem ser ser ilizáveis pela plataforma. Em especial, devem ser adjudicados por contratos de [dados.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### <a name="the-actor-proxy"></a>O representante do ator
A API cliente de Atores Confiáveis fornece comunicação entre uma instância de ator e um cliente ator. Para comunicar com um ator, um cliente cria um objeto de procuração de ator que implementa a interface do ator. O cliente interage com o ator invocando métodos sobre o objeto de procuração. O representante do ator pode ser usado para comunicação cliente-ator e actor-ator.

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


Note que as duas informações usadas para criar o objeto proxy do ator são o identificação do ator e o nome da aplicação. O ator identifica exclusivamente o ator, enquanto o nome da aplicação identifica a [aplicação Service Fabric](service-fabric-reliable-actors-platform.md#application-model) onde o ator é implantado.

A `ActorProxy`classe (C#) / `ActorProxyBase`(Java) do lado do cliente realiza a resolução necessária para localizar o ator por ID e abrir um canal de comunicação com ele. Também tenta localizar o ator em casos de falhas de comunicação e falhas. Como resultado, a entrega da mensagem tem as seguintes características:

* A entrega de mensagens é o melhor esforço.
* Os atores podem receber mensagens duplicadas do mesmo cliente.

## <a name="concurrency"></a>Simultaneidade
O tempo de execução dos Atores Fiáveis fornece um modelo de acesso simples baseado em turnos para aceder aos métodos dos atores. Isto significa que não mais do que um fio pode estar ativo dentro do código de um objeto ator a qualquer momento. O acesso por turnos simplifica consideravelmente os sistemas simultâneos, uma vez que não há necessidade de mecanismos de sincronização para o acesso dos dados. Significa também que os sistemas devem ser concebidos com considerações especiais para a natureza de acesso de cada ator.

* Um único caso de ator não pode processar mais do que um pedido de cada vez. Uma instância de ator pode causar um estrangulamento de entrada se se espera que lide com pedidos simultâneos.
* Os atores podem travar um ao outro se houver um pedido circular entre dois atores, enquanto um pedido externo é feito a um dos atores simultaneamente. O tempo de funcionação do ator irá automaticamente cronometrar as chamadas do ator e lançar uma exceção ao ouvinte para interromper possíveis situações de impasse.

![Comunicação de Atores Fiáveis][3]

### <a name="turn-based-access"></a>Acesso à turn-based
Uma volta consiste na execução completa de um método de ator em resposta a um pedido de outros atores ou clientes, ou a execução completa de um [temporizador/lembrete](service-fabric-reliable-actors-timers-reminders.md) de chamada. Mesmo que estes métodos e chamadas sejam assíncronos, o tempo de execução dos Atores não os intercala. Uma volta deve ser completada antes de ser permitida uma nova volta. Por outras palavras, um método de ator ou chamada de lembrete que esteja atualmente a ser executado deve ser totalmente terminado antes de ser permitida uma nova chamada para um método ou chamada. Considera-se que um método ou recuo terminou se a execução tiver regressado do método ou do backback e a tarefa devolvida pelo método ou pelo backback tiver terminado. Vale a pena sublinhar que a moeda baseada em turnos é respeitada mesmo em diferentes métodos, tempos e chamadas.

O tempo de execução dos Atores impõe a conmoeda baseada na viragem, adquirindo uma fechadura por ator no início de uma curva e libertando o cadeado no final da curva. Assim, a moeda baseada em turnos é aplicada numa base por ator e não entre os atores. Os métodos do ator e as chamadas de lembrete/lembrete podem ser executadas simultaneamente em nome de diferentes atores.

O exemplo que se segue ilustra os conceitos acima referidos. Considere um tipo de ator que implemente dois métodos assíncronos (por exemplo, *Método1* e *Método2),* um temporizador e um lembrete. O diagrama abaixo mostra um exemplo de uma linha temporal para a execução destes métodos e chamadas em nome de dois atores (*ActorId1* e *ActorId2*) que pertencem a este tipo de ator.

![Fiável Atores com tempo de viragem conmoeda e acesso][1]

Este diagrama segue estas convenções:

* Cada linha vertical mostra o fluxo lógico de execução de um método ou uma chamada em nome de um ator em particular.
* Os eventos marcados em cada linha vertical ocorrem por ordem cronológica, com eventos mais recentes ocorrendo abaixo dos mais antigos.
* Cores diferentes são usadas para linhas de tempo correspondentes a diferentes atores.
* O destaque é utilizado para indicar a duração da qual o bloqueio por ator é mantido em nome de um método ou de um backback.

Alguns pontos importantes a ter em conta:

* Enquanto o *Method1* está a executar em nome do *ActorId2* em resposta ao pedido do cliente *xyz789*, chega outro pedido de cliente *(abc123)* que também requer que o *Método1* seja executado pelo *ActorId2*. No entanto, a segunda execução do *Método1* só começa quando a execução prévia tiver terminado. Da mesma forma, um lembrete registado pelos incêndios *do ActorId2* enquanto o *Método1* está a ser executado em resposta ao pedido do cliente *xyz789*. O callback lembrete só é executado depois de ambas as execuções do *Método 1* estarem completas. Tudo isto deve-se à aplicação da moeda baseada em turnos para *o ActorId2*.
* Da mesma forma, a conmoeda baseada em turnos também é aplicada para *o ActorId1*, como demonstra a execução do *Método 1*, *Método2*, e o temporizador de chamada em nome do *ActorId1* acontecendo de forma série.
* A execução do *Método1* em nome do *ActorId1* sobrepõe-se à sua execução em nome do *ActorId2*. Isto porque a moeda baseada na viragem é aplicada apenas dentro de um ator e não entre atores.
* Em algumas das execuções de `Task`método/backback, `CompletableFuture`o (C#) / (Java) devolvido pelo método/retorno após o retorno do método. Em alguns outros, a operação assíncrona já terminou quando o método/retorno de retorno. Em ambos os casos, o bloqueio por ator só é libertado após o retorno do método/callback e os acabamentos de operação assíncronos.

### <a name="reentrancy"></a>Reentrada
O tempo de execução dos Atores permite a reentrabilidade por defeito. Isto significa que se um método de ator do *ator A* chamar um método ao *Ator B*, que por sua vez chama outro método ao Ator *A*, esse método é permitido executar. Isto porque faz parte do mesmo contexto lógico de call-chain. Todas as chamadas de temporizador e lembrete começam com o novo contexto de chamada lógica. Consulte a [reentração de Atores Fiáveis](service-fabric-reliable-actors-reentrancy.md) para mais detalhes.

### <a name="scope-of-concurrency-guarantees"></a>Âmbito das garantias de moedas
O tempo de funcionação dos atores fornece estas garantias de condivisa em situações em que controla a invocação destes métodos. Por exemplo, fornece estas garantias para o método invocações que são feitas em resposta a um pedido do cliente, bem como para chamadas de temporizador e lembrete. No entanto, se o código do ator invoca diretamente estes métodos fora dos mecanismos fornecidos pelos Atores, então o tempo de execução não pode fornecer quaisquer garantias de condivisa. Por exemplo, se o método for invocado no contexto de alguma tarefa que não esteja associada à tarefa devolvida pelos métodos do ator, então o tempo de execução não pode fornecer garantias de condivisa. Se o método for invocado a partir de um fio que o ator cria por si só, então o tempo de execução também não pode fornecer garantias de condivisões. Por isso, para realizar operações de fundo, os atores devem usar [os temporizadores dos atores e lembretes](service-fabric-reliable-actors-timers-reminders.md) de ator estoiradores que respeitem a conmoeda baseada na viragem.

## <a name="next-steps"></a>Passos seguintes
Inicie-se construindo o seu primeiro serviço Deatores Fiáveis:
   * [Começando com Atores Fiáveis em .NET](service-fabric-reliable-actors-get-started.md)
   * [Começando com Atores Fiáveis em Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
