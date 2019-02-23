---
title: Descrição geral do ciclo de vida do Reliable Services do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre os eventos de ciclo de vida diferente no Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5a47e288059cb65615a47e2e1c472178090b0818
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737408"
---
# <a name="reliable-services-lifecycle-overview"></a>Descrição geral de ciclo de vida do Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando estiver pensando em ciclos de vida do Reliable Services do Azure Service Fabric, as noções básicas do ciclo de vida são mais importantes. Em geral, o ciclo de vida inclui o seguinte:

- Durante a inicialização:
  - Os serviços são construídos.
  - Os serviços têm uma oportunidade para construir e retornar zero ou mais serviços de escuta.
  - Quaisquer serviços de escuta retornados são abertos, que permite a comunicação com o serviço.
  - O serviço **RunAsync** método é chamado, permitindo que o serviço para realizar tarefas de longa execução ou de trabalho em segundo plano.
- Durante o encerramento:
  - O token de cancelamento transmitidos para **RunAsync** é cancelada, e os serviços de escuta estão fechados.
  - Depois de fechar os serviços de escuta, o objeto de serviço em si é destructed.

Existem detalhes sobre a ordem exata desses eventos. A ordem dos eventos, pode alterar ligeiramente dependendo de Reliable Service ser com ou sem estado. Além disso, para serviços com estado, podemos deve lidar com o cenário de troca primário. Durante essa seqüência, a função de principal é transferida para outra réplica (ou volta) sem o serviço a ser encerrado. Por fim, podemos tem de pensar sobre condições de erro ou falha.

## <a name="stateless-service-startup"></a>Arranque do serviço sem estado
O ciclo de vida de um serviço sem estado é simples. Esta é a ordem dos eventos:

1. O serviço é construído.
2. Em seguida, em paralelo, acontecem duas coisas:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e qualquer devolvido de serviços de escuta estão abertos. `ICommunicationListener.OpenAsync()` é chamado em cada serviço de escuta.
    - O serviço `StatelessService.RunAsync()` método é chamado.
3. Se estiver presente, o serviço `StatelessService.OnOpenAsync()` método é chamado. Esta chamada é uma substituição incomum, mas está disponível. Tarefas de inicialização do serviço expandida podem ser iniciadas neste momento.

Lembre-se de que não existe nenhum ordenação entre as chamadas para criar e abrir os serviços de escuta e **RunAsync**. Os serviços de escuta podem abrir antes **RunAsync** é iniciado. Da mesma forma, pode invocar **RunAsync** antes dos serviços de escuta de comunicação são abertas ou até mesmo construído. Se qualquer sincronização for necessária, é deixado como um exercício para o implementador. Aqui estão algumas soluções comuns:

  - Por vezes, serviços de escuta não podem funcionar até que algumas outras informações são criadas ou trabalho é feito. Para serviços sem estado, que normalmente pode ser feito o trabalho em outros locais, como o seguinte: 
    - No construtor do serviço.
    - Durante o `CreateServiceInstanceListeners()` chamar.
    - Como parte da construção do serviço de escuta em si.
  - Por vezes, o código na **RunAsync** não começa até que os serviços de escuta estão abertos. Neste caso, coordenação adicional é necessária. Uma solução comum é que existe um sinalizador dentro os serviços de escuta que indica quando tiver concluído. Este sinalizador estiver marcado, em seguida, na **RunAsync** antes de continuar com o trabalho real.

## <a name="stateless-service-shutdown"></a>Encerramento do serviço sem estado
Para encerrar um serviço sem estado, o mesmo padrão é seguido, just-in inversa:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento é passado para `RunAsync()` é cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade retorna true e se chamado, o token `ThrowIfCancellationRequested` método lança uma `OperationCanceledException`.
2. Após `CloseAsync()` termina em cada serviço de escuta e `RunAsync()` também estiver concluída, o serviço `StatelessService.OnCloseAsync()` método é chamado, se estiver presente.  OnCloseAsync é chamado quando a instância de serviço sem estado vai ser for encerrada corretamente. Isto pode ocorrer quando o código do serviço está a ser atualizado, a instância de serviço está a ser movida devido ao balanceamento de carga ou for detetada uma falha transitória. É raro que substituir `StatelessService.OnCloseAsync()`, mas pode ser usado com segurança, fechar recursos, parar processamento em segundo plano, concluir a guardar Estado externo ou fechar as ligações existentes.
3. Depois de `StatelessService.OnCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-startup"></a>Arranque do serviço com estado
Serviços com estado tem um padrão semelhante aos serviços sem estado, com algumas alterações. Para iniciar um serviço com estado, a ordem dos eventos é o seguinte:

1. O serviço é construído.
2. `StatefulServiceBase.OnOpenAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.
3. As seguintes ações ocorrem em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado. 
      - Se o serviço é um serviço principal, todos os serviços de escuta retornados são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada serviço de escuta.
      - Se o serviço é um serviço secundário, apenas esses serviços de escuta é marcado como `ListenOnSecondary = true` estão abertas. É menos comum ter ouvintes de que estão abertas no bases de dados secundárias.
    - Se o serviço está atualmente um site primário, o serviço `StatefulServiceBase.RunAsync()` método é chamado.
4. Depois de todas o réplicas do serviço de escuta `OpenAsync()` chama finish e `RunAsync()` é chamado, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

Semelhante aos serviços sem estado, não há nenhum coordenação entre a ordem na qual os serviços de escuta são criados e abertos e quando **RunAsync** é chamado. Se precisar de coordenação, as soluções são muito parecido com isso. Há um caso adicional para serviço com estado. Digamos que as chamadas que deparar-se com os serviços de escuta de comunicação requerem que as informações mantidas dentro de alguns [Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Uma vez que os serviços de escuta de comunicação foi possível abrir o antes de coleções fiáveis são legíveis ou gravável e antes **RunAsync** foi possível iniciar alguns coordenação adicional é necessária. A solução mais simples e mais comum é para os serviços de escuta de comunicação retornar um código de erro que o cliente utiliza de saber para repetir o pedido.

## <a name="stateful-service-shutdown"></a>Encerramento do serviço com estado
Como serviços sem estado, os eventos de ciclo de vida durante o desligamento são os mesmos durante a inicialização, mas invertido. Quando um serviço com estado está a ser encerrado, ocorrem os seguintes eventos:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento é passado para `RunAsync()` é cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade retorna true e se chamado, o token `ThrowIfCancellationRequested` método lança uma `OperationCanceledException`.
2. Após `CloseAsync()` termina em cada serviço de escuta e `RunAsync()` também estiver concluída, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

   > [!NOTE]  
   > A necessidade de aguardar **RunAsync** concluir, é necessário apenas se esta réplica é uma réplica primária.

3. Depois do `StatefulServiceBase.OnChangeRoleAsync()` conclusão do método, o `StatefulServiceBase.OnCloseAsync()` método é chamado. Esta chamada é uma substituição incomum, mas está disponível.
3. Depois de `StatefulServiceBase.OnCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-primary-swaps"></a>Trocas de principal de serviço com estado
Durante a execução de um serviço com estado, as réplicas primárias de que os serviços com estado tem seus serviços de escuta de comunicação abertos e seus **RunAsync** método chamado. As réplicas secundárias são construídas, mas não mais chamadas de ver. Durante a execução de um serviço com estado, pode alterar a réplica que atualmente é o principal devido a falhas ou cluster de balanceamento de otimização. O que isso significa em termos dos eventos de ciclo de vida que uma réplica pode ver? O comportamento que vê a réplica com monitoração de estado depende se é a réplica a ser despromovida ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para as primárias que estão a ser despromovida
Para a réplica primária, que está a ser despromovida, o Service Fabric tem esta réplica para parar o processamento das mensagens e sair de qualquer trabalho em segundo plano, que ele está fazendo. Como resultado, este passo é como era quando o serviço é encerrado. Uma diferença é que o serviço não estiver destructed ou fechado porque ele continua sendo como um secundário. As seguintes APIs são chamadas:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento é passado para `RunAsync()` é cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade retorna true e se chamado, o token `ThrowIfCancellationRequested` método lança uma `OperationCanceledException`.
2. Após `CloseAsync()` termina em cada serviço de escuta e `RunAsync()` também estiver concluída, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para o secundário que é promovido
Da mesma forma, o Service Fabric tem a réplica secundária que é promovida para começar a ouvir as mensagens na conexão e iniciar quaisquer tarefas em segundo plano que precisa para concluir. Como resultado, esse processo parece que necessitaria se fosse o serviço é criado, exceto pelo fato da réplica em si já existe. As seguintes APIs são chamadas:

1. Em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e qualquer devolvido de serviços de escuta estão abertos. `ICommunicationListener.OpenAsync()` é chamado em cada serviço de escuta.
    - O serviço `StatefulServiceBase.RunAsync()` método é chamado.
2. Depois de todas o réplicas do serviço de escuta `OpenAsync()` chama finish e `RunAsync()` é chamado, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o encerramento do serviço com monitorização de estado e despromoção primária
Service Fabric altera o principal de serviço com estado para uma variedade de motivos. A mais comum são [cluster reequilibrar](service-fabric-cluster-resource-manager-balancing.md) e [atualização da aplicação](service-fabric-application-upgrade.md). Durante essas operações (bem como durante o encerramento do serviço normal, como veria se o serviço foi eliminado), é importante que o serviço respeita o `CancellationToken`. 

Serviços que não lidar com cancelamento corretamente podem assistir a vários problemas. Essas operações são lentas, porque o Service Fabric aguarda para os serviços parar corretamente. Por fim, isso pode levar a atualizações com falha desse tempo limite e revertê-lo. Falha que respeite o token de cancelamento também pode causar desequilibrados clusters. Clusters ficarem desequilibrados porque nós obtém acesso frequente, mas os serviços não podem ser reequilibrados porque demora muito tempo para movê-los em outro lugar. 

Como os serviços são com monitoração de estado, também é provável que utilizam o [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um site primário é despromovido, uma das primeiras coisas que acontece é que o acesso de escrita para o estado subjacente é revogado. Isso nos leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. A coleções de retornadas de exceções com base no período de tempo e se a réplica está a ser movida ou encerramento. Essas exceções devem ser manipuladas corretamente. Exceções geradas pelo Service Fabric enquadram permanente [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitório [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) categorias. Permanentes exceções devem ser registadas e geradas enquanto as exceções transitórias podem ser repetidas com base em algumas lógicas de repetição.

Lidar com as exceções que vêm da utilização do `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma parte importante de testar e validar a um serviço fiável. Recomendamos que execute sempre o seu serviço sob carga durante a realização de atualizações e [chaos teste](service-fabric-controlled-chaos.md) antes da implantação em produção. Estas etapas básicas ajudam a garantir que seu serviço é implementado corretamente e que processa os eventos de ciclo de vida corretamente.


## <a name="notes-on-the-service-lifecycle"></a>Notas sobre o ciclo de vida do serviço
  - Ambas as `RunAsync()` método e o `CreateServiceReplicaListeners/CreateServiceInstanceListeners` chamadas são opcionais. Um serviço pode ter um dos-los, ambos ou nenhum deles. Por exemplo, se o serviço faz todo o trabalho em resposta a chamadas de utilizador, não é necessário para o mesmo implementar `RunAsync()`. Apenas os serviços de escuta de comunicação e o respetivo código associado são necessários. Da mesma forma, criação e o retorno de serviços de escuta de comunicação são opcional, porque o serviço pode ter apenas trabalho em segundo plano para o fazer e então, apenas tem de implementar `RunAsync()`.
  - Ele é válido para um serviço concluir `RunAsync()` com êxito e retorno do mesmo. A conclusão não é uma condição de falha. Concluir `RunAsync()` indica que o trabalho em segundo plano do serviço foi concluída. Para reliable services com estado, `RunAsync()` é chamado novamente, se a réplica é despromovida de principal para secundária e, em seguida, é promovida para principal.
  - Se sair de um serviço de `RunAsync()` lançando uma exceção inesperada, isso constitui uma falha. O objeto de serviço é encerrado e é comunicado um erro de estado de funcionamento.
  - Embora não exista nenhum limite de tempo no retorno entre esses métodos, imediatamente perde a capacidade de escrever para coleções fiáveis e, por conseguinte, não é possível concluir todo o trabalho real. Recomendamos que retornar o mais rapidamente possível após receber a solicitação de cancelamento. Se o seu serviço não responder a essas chamadas de API num período de tempo razoável, o Service Fabric pode terminar a forçar seu serviço. Normalmente, isto só acontece durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por predefinição.
  - Falhas no `OnCloseAsync()` resultado de caminho no `OnAbort()` a ser chamado, o que é uma oportunidade de melhor esforço de última oportunidade para o serviço de limpeza e liberar quaisquer recursos que eles solicitou. Isso geralmente é chamado quando for detetada uma falha de permanente no nó, ou quando o Service Fabric com confiança não é possível gerir o ciclo de vida da instância de serviço devido a falhas internas.
  - `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está mudando função, por exemplo para a primária ou secundária. Réplicas primárias recebem o estado de escrita (têm permissão para criar e escrever Reliable Collections). As réplicas secundárias recebem o estado de leitura (só pode ler a partir de coleções fiáveis existente). Maior parte do trabalho num serviço com monitorização de estado é efetuada na réplica primária. As réplicas secundárias podem efetuar a validação de só de leitura, geração de relatórios, mineração de dados ou outras tarefas só de leitura.

## <a name="next-steps"></a>Passos Seguintes
- [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
- [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
