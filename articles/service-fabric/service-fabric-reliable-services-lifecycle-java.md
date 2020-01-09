---
title: Ciclo de vida de Reliable Services de Service Fabric do Azure
description: Saiba mais sobre os eventos de ciclo de vida em um aplicativo de Reliable Services de Service Fabric do Azure usando Java para serviços com e sem estado.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639603"
---
# <a name="reliable-services-lifecycle"></a>Ciclo de vida dos Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services é um dos modelos de programação disponíveis no Service Fabric do Azure. Ao aprender sobre o ciclo de vida do Reliable Services, é mais importante entender os eventos básicos do ciclo de vida. A ordem exata dos eventos depende dos detalhes de configuração. 

Em geral, o ciclo de vida de Reliable Services inclui os seguintes eventos:

* Durante a inicialização:
  * Os serviços são construídos.
  * Os serviços têm a oportunidade de construir e retornar zero ou mais ouvintes.
  * Todos os ouvintes retornados são abertos para comunicação com o serviço.
  * O método de `runAsync` do serviço é chamado, portanto, o serviço pode executar trabalho de longa execução ou em segundo plano.
* Durante o desligamento:
  * O token de cancelamento que foi passado para `runAsync` é cancelado e os ouvintes são fechados.
  * O próprio objeto de serviço é destruído.

A ordem dos eventos em Reliable Services pode mudar um pouco dependendo se o serviço confiável é com ou sem monitoração de estado. 

Além disso, para serviços com estado, você deve abordar o cenário de permuta primário. Durante essa sequência, a função de primária é transferida para outra réplica (ou retorna) sem que o serviço seja desligado. 

Finalmente, você precisa pensar nas condições de erro ou falha.

## <a name="stateless-service-startup"></a>Inicialização do serviço sem estado
O ciclo de vida de um serviço sem estado é razoavelmente simples. Aqui está a ordem dos eventos:

1. O serviço é construído.
2. Esses eventos ocorrem em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado e todos os ouvintes retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método de `runAsync` do serviço (`StatelessService.runAsync()`) é chamado.
3. Se presente, o método do próprio serviço `onOpenAsync` será chamado. Especificamente, `StatelessService.onOpenAsync()` é chamado. Esta é uma substituição incomum, mas está disponível.

É importante observar que não há nenhuma ordem entre a chamada para criar e abrir os ouvintes e a chamada para `runAsync`. Os ouvintes podem abrir antes de `runAsync` ser iniciado. Da mesma forma, `runAsync` pode ser invocado antes que os ouvintes de comunicação sejam abertos ou antes de terem sido construídos. Se qualquer sincronização for necessária, ela deverá ser feita pelo implementador. Aqui estão algumas soluções comuns:

* Às vezes, os ouvintes não funcionam até que outras informações sejam criadas ou outro trabalho seja feito. Para serviços sem estado, esse trabalho geralmente pode ser feito no construtor do serviço. Isso pode ser feito durante a chamada de `createServiceInstanceListeners()` ou como parte da construção do ouvinte em si.
* Às vezes, o código em `runAsync` não será iniciado até que os ouvintes sejam abertos. Nesse caso, a coordenação adicional é necessária. Uma solução comum é adicionar um sinalizador nos ouvintes. O sinalizador indica quando os ouvintes foram concluídos. O método `runAsync` verifica isso antes de continuar o trabalho real.

## <a name="stateless-service-shutdown"></a>Desligamento de serviço sem estado
Ao desligar um serviço sem estado, o mesmo padrão é seguido, mas na ordem inversa:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento que foi passado para `runAsync()` é cancelado. Verificar a propriedade `isCancelled` do token de cancelamento retorna `true`e, se chamado, o método de `throwIfCancellationRequested` do token lança uma `CancellationException`.
2. Quando `closeAsync()` for concluída em cada ouvinte e `runAsync()` também for concluída, o método de `StatelessService.onCloseAsync()` do serviço será chamado, se estiver presente. Novamente, essa não é uma substituição comum, mas pode ser usada para fechar recursos com segurança, parar o processamento em segundo plano, terminar de salvar o estado externo ou fechar conexões existentes.
3. Depois que `StatelessService.onCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-startup"></a>Inicialização do serviço com estado
Os serviços com estado têm um padrão semelhante aos serviços sem estado, com algumas alterações.  Aqui está a ordem dos eventos para iniciar um serviço com estado:

1. O serviço é construído.
2. `StatefulServiceBase.onOpenAsync()` é chamado. Essa chamada não é comumente substituída no serviço.
3. Esses eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes retornados serão abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, somente os ouvintes marcados como `listenOnSecondary = true` serão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for um primário no momento, o método de `StatefulServiceBase.runAsync()` do serviço será chamado.
4. Depois que todas as chamadas de `openAsync()` do ouvinte de réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não é comumente substituída no serviço.

Semelhante aos serviços sem estado, em serviço com estado, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando `runAsync` é chamado. Se você precisar de coordenação, as soluções são muito semelhantes. Mas há um caso adicional para o serviço com estado. Digamos que as chamadas que chegam aos ouvintes de comunicação exigem informações mantidas dentro de algumas [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md). Como os ouvintes de comunicação podem ser abertos antes de as coleções confiáveis serem legíveis ou graváveis e, antes do `runAsync` ser iniciado, é necessária uma coordenação adicional. A solução mais simples e mais comum é que os ouvintes de comunicação retornem um código de erro. O cliente usa o código de erro para tentar a solicitação novamente.

## <a name="stateful-service-shutdown"></a>Desligamento de serviço com estado
Como os serviços sem estado, os eventos do ciclo de vida durante o desligamento são os mesmos que durante a inicialização, mas invertidos. Quando um serviço com estado está sendo desligado, ocorrem os seguintes eventos:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento que foi passado para `runAsync()` é cancelado. Uma chamada para o método `isCancelled()` do token de cancelamento retorna `true`e, se chamado, o método de `throwIfCancellationRequested()` do token gera um `OperationCanceledException`.
2. Depois que `closeAsync()` for concluída em cada ouvinte e `runAsync()` também for concluída, a `StatefulServiceBase.onChangeRoleAsync()` do serviço será chamada. Essa chamada não é comumente substituída no serviço.

   > [!NOTE]  
   > Aguardar a conclusão de `runAsync` será necessário somente se essa réplica for uma réplica primária.

3. Após a conclusão do método de `StatefulServiceBase.onChangeRoleAsync()`, o método `StatefulServiceBase.onCloseAsync()` é chamado. Esta chamada é uma substituição incomum, mas está disponível.
3. Depois que `StatefulServiceBase.onCloseAsync()` for concluído, o objeto de serviço será destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviço com estado
Enquanto um serviço com estado está em execução, os ouvintes de comunicação são abertos e o método `runAsync` é chamado apenas para as réplicas primárias desses serviços com estado. As réplicas secundárias são construídas, mas não confira nenhuma chamada adicional. Enquanto um serviço com estado está em execução, a réplica que é a primária no momento pode mudar. Os eventos de ciclo de vida que uma réplica com estado pode ver dependem de se ela é a réplica que está sendo rebaixada ou promovida durante a permuta.

### <a name="for-the-demoted-primary"></a>Para o primário rebaixado
Service Fabric precisa da réplica primária rebaixada para interromper o processamento de mensagens e parar qualquer trabalho em segundo plano. Esta etapa é semelhante a quando o serviço é desligado. Uma diferença é que o serviço não é destruído ou fechado, pois permanece como um secundário. Ocorrerem os seguintes eventos:

1. Esses eventos ocorrem em paralelo:
    - Todos os ouvintes abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O token de cancelamento que foi passado para `runAsync()` é cancelado. Uma verificação do método de `isCancelled()` do token de cancelamento retorna `true`. Se chamado, o método de `throwIfCancellationRequested()` do token gera um `OperationCanceledException`.
2. Depois que `closeAsync()` for concluída em cada ouvinte e `runAsync()` também for concluída, a `StatefulServiceBase.onChangeRoleAsync()` do serviço será chamada. Essa chamada não é comumente substituída no serviço.

### <a name="for-the-promoted-secondary"></a>Para o secundário promovido
Da mesma forma, Service Fabric precisa da réplica secundária que é promovida para iniciar a escuta de mensagens na conexão e para iniciar todas as tarefas em segundo plano que precisam ser concluídas. Esse processo é semelhante a quando o serviço é criado. A diferença é que a réplica em si já existe. Ocorrerem os seguintes eventos:

1. Esses eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado e todos os ouvintes retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método de `StatefulServiceBase.runAsync()` do serviço é chamado.
2. Depois que todas as chamadas de `openAsync()` do ouvinte de réplica forem concluídas e `runAsync()` for chamado, `StatefulServiceBase.onChangeRoleAsync()` será chamado. Essa chamada não é comumente substituída no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o desligamento do serviço com estado e o rebaixamento primário
Service Fabric altera o primário de um serviço com estado por vários motivos. Os motivos mais comuns são o [rebalanceamento de cluster](service-fabric-cluster-resource-manager-balancing.md) e a [atualização de aplicativos](service-fabric-application-upgrade.md). Durante essas operações, é importante que o serviço respeite a `cancellationToken`. Isso também se aplica durante o desligamento normal do serviço, como se o serviço foi excluído.

Os serviços que não tratam o cancelamento corretamente podem enfrentar vários problemas. Essas operações são lentas porque Service Fabric aguarda que os serviços parem normalmente. Isso, em última análise, pode levar a atualizações com falha e a reversão. A falha em honrar o token de cancelamento também pode causar clusters desequilibrados. Os clusters tornam-se desequilibrados porque os nós ficam quentes. No entanto, os serviços não podem ser rebalanceados porque leva muito tempo para movê-los para outro lugar. 

Como os serviços têm estado, também é provável que os serviços usem [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um primário é rebaixado, uma das primeiras coisas que acontece é que o acesso de gravação ao estado subjacente é revogado. Isso leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. As coleções retornam exceções com base no tempo e se a réplica está sendo movida ou desligada. É importante tratar essas exceções corretamente. 

Exceções geradas por Service Fabric são permanentes [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) ou transitórias [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Exceções permanentes devem ser registradas em log e geradas. Exceções transitórias podem ser repetidas com base na lógica de repetição.

Uma parte importante do teste e da validação de Reliable Services é manipular as exceções que vêm do uso do `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço. É recomendável que você sempre execute o serviço sob carga. Você também deve executar atualizações e [testes de caos](service-fabric-controlled-chaos.md) antes de implantar na produção. Essas etapas básicas ajudam a garantir que o serviço seja implementado corretamente e que ele manipule eventos do ciclo de vida corretamente.

## <a name="notes-on-service-lifecycle"></a>Observações sobre o ciclo de vida do serviço
* O método `runAsync()` e as chamadas `createServiceInstanceListeners/createServiceReplicaListeners` são opcionais. Um serviço pode ter um, ambos ou nenhum. Por exemplo, se o serviço faz todo seu trabalho em resposta a chamadas de usuário, não há necessidade de implementar `runAsync()`. Somente os ouvintes de comunicação e seus códigos associados são necessários.  Da mesma forma, a criação e o retorno de ouvintes de comunicação são opcionais. O serviço pode ter apenas trabalho em segundo plano para fazer, portanto, ele só precisa implementar `runAsync()`.
* É válido para um serviço concluir `runAsync()` com êxito e retornar dele. Isso não é considerado uma condição de falha. Representa o trabalho em segundo plano da conclusão do serviço. Para Reliable Services com estado, `runAsync()` seria chamado novamente se o serviço for rebaixado do primário e promovido novamente para primário.
* Se um serviço sair de `runAsync()` lançando uma exceção inesperada, isso é uma falha. O objeto de serviço é desligado e um erro de integridade é relatado.
* Embora não haja nenhum limite de tempo ao retornar desses métodos, você perde imediatamente a capacidade de escrever. Portanto, você não pode concluir qualquer trabalho real. É recomendável que você retorne o mais rápido possível após receber a solicitação de cancelamento. Se o serviço não responder a essas chamadas à API em um período de tempo razoável, Service Fabric poderá forçar o encerramento do serviço. Normalmente, isso ocorre apenas durante as atualizações do aplicativo ou quando um serviço está sendo excluído. Esse tempo limite é de 15 minutos por padrão.
* As falhas no `onCloseAsync()` caminho resultam na chamada `onAbort()`. Essa chamada é uma oportunidade de última chance e melhor esforço para que o serviço seja limpo e libere os recursos que eles declararam. Isso é geralmente chamado quando uma falha permanente é detectada no nó ou quando Service Fabric não pode gerenciar de maneira confiável o ciclo de vida da instância de serviço devido a falhas internas.
* `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está alterando a função, por exemplo, para primário ou secundário. As réplicas primárias recebem status de gravação (têm permissão para criar e gravar em coleções confiáveis). As réplicas secundárias recebem status de leitura (só podem ser lidas de coleções confiáveis existentes). A maior parte do trabalho em um serviço com estado é executada na réplica primária. As réplicas secundárias podem executar validação somente leitura, geração de relatórios, Data Mining ou outros trabalhos somente leitura.

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guia de início rápido Reliable Services](service-fabric-reliable-services-quick-start-java.md)

