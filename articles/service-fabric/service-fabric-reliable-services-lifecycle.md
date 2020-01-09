---
title: Visão geral do ciclo de vida do Reliable Services
description: Saiba mais sobre os eventos do ciclo de vida em um aplicativo Service Fabric do Azure Reliable Services para serviços com e sem estado.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645502"
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do Reliable Services Lifecycle
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando estiver pensando sobre os ciclos de vida do Azure Service Fabric Reliable Services, os conceitos básicos do ciclo de vida são os mais importantes. Em geral, o ciclo de vida inclui o seguinte:

- Durante a inicialização:
  - Os serviços são construídos.
  - Os serviços têm a oportunidade de construir e retornar zero ou mais ouvintes.
  - Todos os ouvintes retornados são abertos, permitindo a comunicação com o serviço.
  - O método **RunAsync** do serviço é chamado, permitindo que o serviço faça tarefas de longa execução ou trabalho em segundo plano.
- Durante o desligamento:
  - O token de cancelamento passado para **RunAsync** é cancelado e os ouvintes são fechados.
  - Depois que os ouvintes fecham, o próprio objeto de serviço é destruído.

Há detalhes sobre a ordenação exata desses eventos. A ordem dos eventos pode ser ligeiramente alterada, dependendo se o serviço confiável é com ou sem estado. Além disso, para serviços com estado, devemos lidar com o cenário de permuta primário. Durante essa sequência, a função de primária é transferida para outra réplica (ou retorna) sem que o serviço seja desligado. Por fim, devemos pensar em condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização do serviço sem estado
O ciclo de vida de um serviço sem estado é simples. Aqui está a ordem dos eventos:

1. O serviço é construído.
2. Em paralelo, duas coisas acontecem:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e todos os ouvintes retornados são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método de `StatelessService.RunAsync()` do serviço é chamado.
3. Se estiver presente, o método de `StatelessService.OnOpenAsync()` do serviço será chamado. Esta chamada é uma substituição incomum, mas está disponível. As tarefas de inicialização do serviço estendido podem ser iniciadas neste momento.

Tenha em mente que não há nenhuma ordem entre as chamadas para criar e abrir os ouvintes e **RunAsync**. Os ouvintes podem abrir antes de o **RunAsync** ser iniciado. Da mesma forma, você pode invocar **RunAsync** antes que os ouvintes de comunicação sejam abertos ou até mesmo construídos. Se qualquer sincronização for necessária, ela será deixada como um exercício para o implementador. Aqui estão algumas soluções comuns:

  - Às vezes, os ouvintes não funcionam até que outras informações sejam criadas ou o trabalho seja feito. Para serviços sem estado, esse trabalho normalmente pode ser feito em outros locais, como o seguinte: 
    - No construtor do serviço.
    - Durante a chamada de `CreateServiceInstanceListeners()`.
    - Como parte da construção do ouvinte em si.
  - Às vezes, o código em **RunAsync** não é iniciado até que os ouvintes sejam abertos. Nesse caso, a coordenação adicional é necessária. Uma solução comum é que há um sinalizador dentro dos ouvintes que indica quando eles foram concluídos. Esse sinalizador é então verificado no **RunAsync** antes de continuar para o trabalho real.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Para desligar um serviço sem estado, o mesmo padrão é seguido, apenas na ordem inversa:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token gera uma `OperationCanceledException`.
2. Depois que `CloseAsync()` for concluída em cada ouvinte e `RunAsync()` também for concluída, o método de `StatelessService.OnCloseAsync()` do serviço será chamado, se estiver presente.  OnCloseAsync é chamado quando a instância do serviço sem estado está prestes a ser desligada normalmente. Isso pode ocorrer quando o código do serviço está sendo atualizado, a instância do serviço está sendo movida devido ao balanceamento de carga ou uma falha transitória é detectada. É incomum substituir `StatelessService.OnCloseAsync()`, mas pode ser usado para fechar recursos com segurança, parar o processamento em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.
3. Depois que `StatelessService.OnCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-startup"></a>Inicialização do serviço com estado
Os serviços com estado têm um padrão semelhante aos serviços sem estado, com algumas alterações. Para iniciar um serviço com estado, a ordem dos eventos é a seguinte:

1. O serviço é construído.
2. `StatefulServiceBase.OnOpenAsync()` é chamado. Essa chamada não é comumente substituída no serviço.
3. As ações a seguir ocorrem em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes retornados serão abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, somente os ouvintes marcados como `ListenOnSecondary = true` serão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for um primário no momento, o método de `StatefulServiceBase.RunAsync()` do serviço será chamado.
4. Depois que todas as chamadas de `OpenAsync()` do ouvinte de réplica forem concluídas e `RunAsync()` for chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não é comumente substituída no serviço.

Semelhante aos serviços sem estado, não há coordenação entre a ordem na qual os ouvintes são criados e abertos e quando **RunAsync** é chamado. Se você precisar de coordenação, as soluções são muito semelhantes. Há um caso adicional para o serviço com estado. Digamos que as chamadas que chegam aos ouvintes de comunicação exigem informações mantidas dentro de algumas [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Como os ouvintes de comunicação podem ser abertos antes de as coleções confiáveis serem legíveis ou graváveis, e antes do **RunAsync** ser iniciado, é necessária alguma coordenação adicional. A solução mais simples e mais comum é que os ouvintes de comunicação retornem um código de erro que o cliente usa para tentar novamente a solicitação.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Como os serviços sem estado, os eventos do ciclo de vida durante o desligamento são os mesmos que durante a inicialização, mas invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token gera uma `OperationCanceledException`.
2. Depois que `CloseAsync()` for concluída em cada ouvinte e `RunAsync()` também for concluída, a `StatefulServiceBase.OnChangeRoleAsync()` do serviço será chamada. Essa chamada não é comumente substituída no serviço.

   > [!NOTE]  
   > A necessidade de aguardar a conclusão de **RunAsync** só será necessária se esta réplica for uma réplica primária.

3. Após a conclusão do método de `StatefulServiceBase.OnChangeRoleAsync()`, o método `StatefulServiceBase.OnCloseAsync()` é chamado. Esta chamada é uma substituição incomum, mas está disponível.
3. Depois que `StatefulServiceBase.OnCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviço com estado
Enquanto um serviço com estado está em execução, somente as réplicas primárias desses serviços com estado têm seus ouvintes de comunicação abertos e seu método **RunAsync** chamado. As réplicas secundárias são construídas, mas não confira nenhuma chamada adicional. Enquanto um serviço com estado está em execução, a réplica que atualmente é a primária pode mudar como resultado da otimização de falha ou de balanceamento de cluster. O que isso significa em termos dos eventos do ciclo de vida que uma réplica pode ver? O comportamento que a réplica com estado vê depende se ela é a réplica que está sendo rebaixada ou promovida durante a permuta.

### <a name="for-the-primary-thats-demoted"></a>Para o primário que é rebaixado
Para a réplica primária rebaixada, Service Fabric precisa que essa réplica interrompa o processamento de mensagens e encerre qualquer trabalho em segundo plano que esteja fazendo. Como resultado, essa etapa é parecida com quando o serviço é desligado. Uma diferença é que o serviço não é destruido ou fechado porque permanece como secundário. As seguintes APIs são chamadas:

1. Em paralelo:
    - Todos os ouvintes abertos são fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O token de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade `IsCancellationRequested` do token de cancelamento retorna true e, se chamado, o método `ThrowIfCancellationRequested` do token gera uma `OperationCanceledException`.
2. Depois que `CloseAsync()` for concluída em cada ouvinte e `RunAsync()` também for concluída, a `StatefulServiceBase.OnChangeRoleAsync()` do serviço será chamada. Essa chamada não é comumente substituída no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para o secundário que é promovido
Da mesma forma, Service Fabric precisa da réplica secundária que é promovida para iniciar a escuta de mensagens na conexão e iniciar todas as tarefas em segundo plano que precisam ser concluídas. Como resultado, esse processo tem a aparência que fazia quando o serviço é criado, exceto que a réplica em si já existe. As seguintes APIs são chamadas:

1. Em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e todos os ouvintes retornados são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método de `StatefulServiceBase.RunAsync()` do serviço é chamado.
2. Depois que todas as chamadas de `OpenAsync()` do ouvinte de réplica forem concluídas e `RunAsync()` for chamado, `StatefulServiceBase.OnChangeRoleAsync()` será chamado. Essa chamada não é comumente substituída no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e o rebaixamento primário
Service Fabric altera o primário de um serviço com estado por vários motivos. As mais comuns são o [rebalanceamento de cluster](service-fabric-cluster-resource-manager-balancing.md) e a [atualização de aplicativos](service-fabric-application-upgrade.md). Durante essas operações (bem como durante o desligamento normal do serviço, como você veria se o serviço foi excluído), é importante que o serviço respeite o `CancellationToken`. 

Os serviços que não tratam o cancelamento corretamente podem ter vários problemas. Essas operações são lentas porque Service Fabric aguarda que os serviços parem normalmente. Isso, por fim, pode levar a atualizações com falha que atingiram o tempo limite e são revertidos. A falha em honrar o token de cancelamento também pode causar clusters desequilibrados. Os clusters tornam-se desequilibrados porque os nós ficam quentes, mas os serviços não podem ser rebalanceados porque leva muito tempo para movê-los para outro lugar. 

Como os serviços têm estado, também é provável que eles usem as [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um primário é rebaixado, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. As coleções retornam exceções com base no tempo e se a réplica está sendo movida ou desligada. Essas exceções devem ser tratadas corretamente. As exceções geradas por Service Fabric se enquadram em categorias permanentes [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitórias [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) . Exceções permanentes devem ser registradas em log e geradas enquanto as exceções transitórias podem ser repetidas com base em alguma lógica de repetição.

Lidar com as exceções provenientes do uso do `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma parte importante do teste e da validação de um serviço confiável. É recomendável que você sempre execute o serviço sob carga ao executar atualizações e [teste de caos](service-fabric-controlled-chaos.md) antes de implantar na produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente e manipule eventos de ciclo de vida corretamente.


## <a name="notes-on-the-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
  - O método `RunAsync()` e as chamadas `CreateServiceReplicaListeners/CreateServiceInstanceListeners` são opcionais. Um serviço pode ter um deles, ambos ou nenhum. Por exemplo, se o serviço faz todo seu trabalho em resposta a chamadas de usuário, não é necessário implementar `RunAsync()`. Somente os ouvintes de comunicação e seus códigos associados são necessários. Da mesma forma, a criação e o retorno de ouvintes de comunicação são opcionais, pois o serviço pode ter apenas trabalho em segundo plano e, portanto, precisa apenas implementar `RunAsync()`.
  - É válido para um serviço concluir `RunAsync()` com êxito e retornar dele. A conclusão não é uma condição de falha. Concluir `RunAsync()` indica que o trabalho em segundo plano do serviço foi concluído. Para Reliable Services com estado, `RunAsync()` será chamado novamente se a réplica for rebaixada do primário para o secundário e, em seguida, promovida de volta para primária.
  - Se um serviço sair de `RunAsync()` lançando uma exceção inesperada, isso constituirá uma falha. O objeto de serviço é desligado e um erro de integridade é relatado.
  - Embora não haja nenhum limite de tempo ao retornar desses métodos, você perde imediatamente a capacidade de gravar em coleções confiáveis e, portanto, não pode concluir nenhum trabalho real. Recomendamos que você retorne o mais rápido possível após receber a solicitação de cancelamento. Se o serviço não responder a essas chamadas de API em um período de tempo razoável, Service Fabric poderá forçar o encerramento do serviço. Geralmente isso ocorre apenas durante as atualizações do aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
  - As falhas no caminho `OnCloseAsync()` resultam na chamada `OnAbort()`, que é uma oportunidade de melhor esforço de última chance para o serviço ser limpo e liberar todos os recursos que eles declararam. Isso é geralmente chamado quando uma falha permanente é detectada no nó ou quando Service Fabric não pode gerenciar de maneira confiável o ciclo de vida da instância de serviço devido a falhas internas.
  - `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está alterando a função, por exemplo, para primário ou secundário. As réplicas primárias recebem status de gravação (têm permissão para criar e gravar em coleções confiáveis). As réplicas secundárias recebem status de leitura (só podem ser lidas de coleções confiáveis existentes). A maior parte do trabalho em um serviço com estado é executada na réplica primária. As réplicas secundárias podem executar validação somente leitura, geração de relatórios, Data Mining ou outros trabalhos somente leitura.

## <a name="next-steps"></a>Passos seguintes
- [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
- [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
