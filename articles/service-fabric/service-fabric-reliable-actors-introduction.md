---
title: Visão geral dos atores fiáveis do tecido de serviço
description: Introdução ao modelo de programação de Atores Fidedigstão do Tecido de Serviço, baseado no padrão de Ator Virtual.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: adb15d995cd2a9fd604aa6b91360adc88a2804e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89007932"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introdução a atores fiáveis de tecido de serviço
Reliable Actors é uma estrutura de aplicação de tecido de serviço baseada no padrão [de ator virtual.](https://research.microsoft.com/en-us/projects/orleans/) A API de API de Atores Fidedignos fornece um modelo de programação de fio único baseado nas garantias de escalabilidade e fiabilidade fornecidas pela Service Fabric.

## <a name="what-are-actors"></a>O que são atores?
Um ator é uma unidade isolada e independente de computação e estado com execução de linha única. O [padrão do ator](https://en.wikipedia.org/wiki/Actor_model) é um modelo computacional para sistemas simultâneos ou distribuídos em que um grande número destes atores podem executar simultaneamente e independentemente uns dos outros. Os atores podem comunicar uns com os outros e podem criar mais atores.

### <a name="when-to-use-reliable-actors"></a>Quando usar atores fiáveis
Service Fabric Reliable Actors é uma implementação do padrão de design do ator. Como em qualquer padrão de design de software, a decisão de usar um padrão específico é tomada com base no facto de um problema de design de software se adequa ou não ao padrão.

Embora o padrão de design do ator possa ser um bom ajuste a uma série de problemas e cenários distribuídos, deve ser feita uma cuidadosa consideração dos constrangimentos do padrão e da estrutura que o implementa. Como orientação geral, considere o padrão do ator para modelar o seu problema ou cenário se:

* O seu espaço problemático envolve um grande número (milhares ou mais) de pequenas, independentes e isoladas unidades de estado e lógica.
* Pretende trabalhar com objetos de linha única que não requerem interação significativa de componentes externos, incluindo o estado de consulta através de um conjunto de atores.
* Os seus casos de ator não bloqueiam chamadas com atrasos imprevisíveis ao emitir operações de E/S.

## <a name="actors-in-service-fabric"></a>Atores em Tecido de Serviço
No Service Fabric, os atores são implementados no quadro de atores fidedigdos: uma estrutura de aplicação baseada em padrões de ator construída em cima dos [Serviços De Serviços Fiáveis de Tecidos.](service-fabric-reliable-services-introduction.md) Cada serviço de ator fiável que escreve é, na verdade, um serviço de confiança dividido e imponente.

Cada ator é definido como um exemplo de um tipo de ator, idêntico à forma como um objeto .NET é um exemplo de um tipo .NET. Por exemplo, pode haver um tipo de ator que implementa a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós através de um cluster. Cada ator deste tipo é identificado exclusivamente por um ator.

## <a name="actor-lifetime"></a>Vida de Ator
Os atores do Service Fabric são virtuais, o que significa que a sua vida não está ligada à sua representação na memória. Como resultado, não precisam de ser explicitamente criados ou destruídos. O tempo de execução dos Reliable Actors ativa automaticamente um ator na primeira vez que recebe um pedido para o ID do ator. Se um ator não for utilizado durante um período de tempo, o tivo fiável recolhe o objeto na memória. Também manterá o conhecimento da existência do ator caso tenha de ser reativado mais tarde. Para mais detalhes, consulte [o ciclo de vida do ator e a recolha de lixo.](service-fabric-reliable-actors-lifecycle.md)

Esta abstração de vida de ator virtual carrega algumas ressalvas como resultado do modelo de ator virtual, e na verdade a implementação de Reliable Actors desvia-se em alguns momentos deste modelo.

* Um ator é automaticamente ativado (fazendo com que um objeto ator seja construído) na primeira vez que uma mensagem é enviada para o seu ID do ator. Após algum período de tempo, o objeto do ator é lixo recolhido. No futuro, usando o ID do ator novamente, faz com que um novo objeto ator seja construído. O estado de um ator sobrevive à vida do objeto quando armazenado no gerente do estado.
* Chamar qualquer método de ator para uma identificação de ator ativa aquele ator. Por esta razão, os tipos de atores têm o seu construtor chamado implicitamente pelo tempo de execução. Portanto, o código do cliente não pode passar parâmetros para o construtor do tipo ator, embora os parâmetros possam ser passados para o construtor do ator pelo próprio serviço. O resultado é que os atores podem ser construídos em um estado parcialmente inicializado no momento em que outros métodos são chamados, se o ator requer parâmetros de inicialização do cliente. Não há um único ponto de entrada para a ativação de um ator do cliente.
* Embora os atores fiáveis criem implicitamente objetos de ator; você tem a capacidade de apagar explicitamente um ator e o seu estado.

## <a name="distribution-and-failover"></a>Distribuição e failover
Para proporcionar escalabilidade e fiabilidade, o Service Fabric distribui os atores por todo o cluster e migra-os automaticamente de nós falhados para os saudáveis, conforme necessário. Esta é uma abstração sobre um [serviço fiável, dividido e imponente.](service-fabric-concepts-partitioning.md) A distribuição, a escalabilidade, a fiabilidade e a falha automática são todas fornecidas devido ao facto de os atores estarem a correr dentro de um serviço fidedigna chamado *Serviço de Ator.*

Os atores são distribuídos pelas divisórias do Serviço de Ator, e essas divisórias são distribuídas pelos nós num cluster de Tecido de Serviço. Cada divisória de serviço contém um conjunto de atores. A Service Fabric gere a distribuição e a falha das divisórias de serviço.

Por exemplo, um serviço de ator com nove divisórias implantadas em três nóns usando a colocação de partição do ator predefinido seria distribuído assim:

![Distribuição de atores fiáveis][2]

O Ator Framework gere o esquema de partição e as definições de alcance chave para si. Isto simplifica algumas escolhas, mas também tem alguma consideração:

* Os Serviços Fiáveis permitem-lhe escolher um esquema de partição, um intervalo de chaves (quando se utiliza um esquema de partição de gama) e a contagem de divisórias. Os Atores Fiáveis estão restritos ao esquema de partição de gama (o esquema int64 uniforme) e exigem que utilize toda a gama de chaves Int64.
* Por defeito, os atores são colocados aleatoriamente em divisórias, resultando numa distribuição uniforme.
* Uma vez que os atores são colocados aleatoriamente, é de esperar que as operações dos atores exijam sempre comunicação em rede, incluindo a serialização e a dessessisicionalização dos dados de chamadas de métodos, a latência incorrencial e a sobrecarga.
* Em cenários avançados, é possível controlar a colocação de partição de atores usando IDs de ator Int64 que mapeiam para divisórias específicas. No entanto, fazê-lo pode resultar numa distribuição desequilibrada dos atores através de divisórias.

Para obter mais informações sobre como os serviços de ator são [divididos, consulte conceitos de partição para os atores.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

## <a name="actor-communication"></a>Comunicação do ator
As interações dos atores são definidas numa interface que é partilhada pelo ator que implementa a interface, e o cliente que recebe um proxy para um ator através da mesma interface. Como esta interface é usada para invocar métodos de ator assíncronamente, todos os métodos da interface devem ser o retorno de tarefas.

As invocações de métodos e as suas respostas acabam por resultar em pedidos de rede em todo o cluster, pelo que os argumentos e os tipos de resultados das tarefas que retornam devem ser serializáveis pela plataforma. Em especial, devem ser [contratos de dados serializáveis.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### <a name="the-actor-proxy"></a>O representante do ator
A API, cliente dos Reliable Actors, fornece comunicação entre um caso de ator e um cliente ator. Para comunicar com um ator, um cliente cria um objeto de procuração de ator que implementa a interface do ator. O cliente interage com o ator invocando métodos no objeto de procuração. O proxy ator pode ser usado para comunicação cliente-ator e ator-ator.

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


Note que as duas peças de informação usadas para criar o objeto de procuração do ator são o ID do ator e o nome da aplicação. O ator identifica o ator de forma única, enquanto o nome da aplicação identifica a [aplicação Service Fabric](service-fabric-reliable-actors-platform.md#application-model) onde o ator é implantado.

A `ActorProxy` classe (C#) / `ActorProxyBase` (Java) do lado do cliente realiza a resolução necessária para localizar o ator por ID e abrir um canal de comunicação com ele. Também retrete localizar o ator em casos de falhas de comunicação e falhas. Como resultado, a entrega da mensagem tem as seguintes características:

* A entrega de mensagens é o melhor esforço.
* Os atores podem receber mensagens duplicadas do mesmo cliente.

## <a name="concurrency"></a>Simultaneidade
O tempo de execução do Reliable Actors fornece um modelo de acesso simples baseado em turnos para aceder aos métodos do ator. Isto significa que não mais do que um fio pode estar ativo dentro do código de um objeto ator a qualquer momento. O acesso por turnos simplifica consideravelmente os sistemas simultâneos, uma vez que não há necessidade de mecanismos de sincronização para o acesso aos dados. Significa também que os sistemas devem ser concebidos com considerações especiais para a natureza de acesso de um só roscado de cada instância do ator.

* Uma única instância de ator não pode processar mais do que um pedido de cada vez. Uma instância do ator pode causar um estrangulamento de produção se se espera que lide com pedidos simultâneos.
* Os atores podem travar-se uns aos outros se houver um pedido circular entre dois atores enquanto um pedido externo é feito a um dos atores simultaneamente. O tempo de execução do ator irá automaticamente esgotar as chamadas dos atores e lançar uma exceção ao chamador para interromper possíveis situações de impasse.

![Comunicação de atores fiáveis][3]

### <a name="turn-based-access"></a>Acesso por turnos
Uma vez consiste na execução completa de um método de ator em resposta a um pedido de outros atores ou clientes, ou a execução completa de uma chamada de [temporizador/lembrete.](service-fabric-reliable-actors-timers-reminders.md) Mesmo que estes métodos e chamadas sejam assíncronos, o tempo de execução dos atores não os intercala. Uma volta deve ser totalmente terminada antes de uma nova volta ser permitida. Por outras palavras, um método de ator ou chamada temporizador/lembrete que está atualmente a ser executado deve estar totalmente terminado antes de uma nova chamada para um método ou retorno de chamada é permitido. Um método ou retorno é considerado como tendo terminado se a execução tiver retornado do método ou retorno e a tarefa devolvida pelo método ou retorno de retorno terminou. Vale a pena sublinhar que a concordância baseada na viragem é respeitada mesmo em diferentes métodos, temporizadores e callbacks.

O tempo de execução dos Atores impõe a concordância baseada na curva, adquirindo uma fechadura por ator no início de uma curva e libertando a fechadura no final da curva. Assim, a concordância baseada na viragem é aplicada numa base por ator e não entre os atores. Os métodos do ator e as chamadas temporizador/lembrete podem ser executadas simultaneamente em nome de diferentes atores.

O exemplo a seguir ilustra os conceitos acima referidos. Considere um tipo de ator que implementa dois métodos assíncronos (por exemplo, *Método1* e *Método2),* um temporizador e um lembrete. O diagrama abaixo mostra um exemplo de uma linha temporal para a execução destes métodos e callbacks em nome de dois atores (*ActorId1* e *ActorId2*) que pertencem a este tipo de ator.

![Atores fiáveis runtime turn-based concurrency e acesso][1]

Este diagrama segue estas convenções:

* Cada linha vertical mostra o fluxo lógico de execução de um método ou uma chamada em nome de um determinado ator.
* Os eventos marcados em cada linha vertical ocorrem por ordem cronológica, com eventos mais recentes a ocorrerem abaixo dos mais antigos.
* Cores diferentes são usadas para cronologias correspondentes a diferentes atores.
* O destaque é utilizado para indicar a duração da fechadura por ator em nome de um método ou de uma chamada.

Alguns pontos importantes a ter em conta:

* Enquanto *a Method1* está a ser executada em nome do *ActorId2* em resposta ao pedido do cliente *xyz789*, chega outro pedido de cliente (*abc123*) que também requer que o *Método1* seja executado pelo *ActorId2*. No entanto, a segunda execução do *Método1* só começa quando a execução anterior terminar. Da mesma forma, um lembrete registado pelos incêndios *do ActorId2* enquanto *o Method1* está a ser executado em resposta ao pedido do cliente *xyz789*. O retorno do lembrete só é executado depois de ambas as execuções do *Método1* estarem completas. Tudo isto deve-se à imposição de uma concordância por turnos para *o ActorId2.*
* Da mesma forma, a concordância baseada na curva também é aplicada para *o ActorId1*, como demonstra a execução de *Method1*, *Method2*, e a chamada do temporizador em nome do *ActorId1* acontecendo de forma em série.
* A execução do *Método1* em nome do *ActorId1* sobrepõe-se à sua execução em nome do *ActorId2*. Isto porque a concordância baseada na viragem é aplicada apenas dentro de um ator e não entre os atores.
* Em algumas das execuções método/callback, o `Task` (C#) / `CompletableFuture` (Java) devolvido pelo método/callback termina após o retorno do método. Em alguns outros, a operação assíncrona já terminou no momento em que o método/retorno de retorno. Em ambos os casos, o bloqueio por ator só é libertado após o retorno do método/retorno e a operação assíncrona termina.

### <a name="reentrancy"></a>Reentrada
O tempo de execução dos atores permite a reentrada por defeito. Isto significa que se um método ator de *Ator A* chama um método para o *Ator B*, que por sua vez chama outro método para o *ator A*, esse método é permitido correr. Isto porque faz parte do mesmo contexto lógico da cadeia de chamadas. Todas as chamadas de temporizador e lembrete começam com o novo contexto lógico de chamada. Consulte a reentrada de [Atores Fidedigtos](service-fabric-reliable-actors-reentrancy.md) para mais detalhes.

### <a name="scope-of-concurrency-guarantees"></a>Âmbito de garantias de conuncy
O tempo de execução dos atores fornece estas garantias de concordância em situações em que controla a invocação destes métodos. Por exemplo, fornece estas garantias para as invocações do método que são feitas em resposta a um pedido do cliente, bem como para chamadas de temporizador e lembrete. No entanto, se o código do ator invocar diretamente estes métodos fora dos mecanismos fornecidos pelos atores, então o tempo de execução não pode fornecer quaisquer garantias de concordância. Por exemplo, se o método for invocado no contexto de alguma tarefa que não esteja associada à tarefa devolvida pelos métodos do ator, então o tempo de execução não pode fornecer garantias de concordância. Se o método for invocado a partir de um fio que o ator cria por si só, então o tempo de execução também não pode fornecer garantias de concordância. Portanto, para realizar operações de fundo, os atores devem usar [temporizadores de ator e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md) que respeitem a concordância baseada em turnos.

## <a name="next-steps"></a>Passos seguintes
Começa por construir o teu primeiro serviço de Atores Fidedignso:
   * [Começando com atores fiáveis em .NET](service-fabric-reliable-actors-get-started.md)
   * [Começar com atores fiáveis em Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
