---
title: Ciclo de vida de serviço azure tecido fiável
description: Conheça os eventos de ciclo de vida numa aplicação Azure Service Fabric Reliable Services utilizando java para serviços apátridas e apátridas.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639603"
---
# <a name="reliable-services-lifecycle"></a>Ciclo de vida dos Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

A Reliable Services é um dos modelos de programação disponíveis no Azure Service Fabric. Ao aprender sobre o ciclo de vida dos Serviços Fiáveis, é mais importante compreender os eventos básicos do ciclo de vida. A encomenda exata dos eventos depende de detalhes de configuração. 

Em geral, o ciclo de vida dos Serviços Fiáveis inclui os seguintes eventos:

* Durante o arranque:
  * Os serviços são construídos.
  * Os serviços têm a oportunidade de construir e devolver zero ou mais ouvintes.
  * Todos os ouvintes devolvidos são abertos para comunicação com o serviço.
  * O método `runAsync` do serviço é chamado, para que o serviço possa fazer trabalhos de longa duração ou de fundo.
* Durante a paragem:
  * O sinal de cancelamento `runAsync` que foi passado está cancelado, e os ouvintes estão fechados.
  * O objeto de serviço em si está destruído.

A ordem dos eventos em Serviços Fiáveis pode mudar ligeiramente dependendo se o serviço fiável é apátrida ou apátrida. 

Além disso, para serviços estatais, deve abordar o cenário de troca primária. Durante esta sequência, o papel da primária é transferido para outra réplica (ou volta) sem que o serviço seja desligado. 

Finalmente, tens de pensar em condições de erro ou de falha.

## <a name="stateless-service-startup"></a>Startup de serviço apátrida
O ciclo de vida de um serviço apátrida é bastante simples. Aqui está a ordem dos acontecimentos:

1. O serviço é construído.
2. Estes eventos ocorrem em paralelo:
    - `StatelessService.createServiceInstanceListeners()`é invocado, e quaisquer ouvintes devolvidos são abertos. `CommunicationListener.openAsync()`é chamado a cada ouvinte.
    - O método`StatelessService.runAsync()` `runAsync` do serviço é chamado.
3. Se presente, o próprio `onOpenAsync` método do serviço é chamado. Especificamente, `StatelessService.onOpenAsync()` chama-se. Esta é uma sobreposição incomum, mas está disponível.

É importante notar que não há ordem entre a chamada para criar e `runAsync`abrir os ouvintes e a chamada para . Os ouvintes `runAsync` podem abrir antes de começar. Da mesma `runAsync` forma, pode ser invocado antes que os ouvintes de comunicação sejam abertos, ou antes mesmo de terem sido construídos. Se for necessária alguma sincronização, deve ser feita pelo implementador. Aqui estão algumas soluções comuns:

* Às vezes, os ouvintes não podem funcionar até que outras informações são criadas ou outro trabalho é feito. Para serviços apátridas, esse trabalho normalmente pode ser feito no construtor do serviço. Pode ser feito `createServiceInstanceListeners()` durante a chamada, ou como parte da construção do próprio ouvinte.
* Às vezes, o código `runAsync` não começa até que os ouvintes estejam abertos. Neste caso, é necessária uma coordenação adicional. Uma solução comum é adicionar uma bandeira aos ouvintes. A bandeira indica quando os ouvintes terminarem. O `runAsync` método verifica-o antes de continuar o trabalho real.

## <a name="stateless-service-shutdown"></a>Paralisação do serviço apátrida
Ao desligar um serviço apátrida, segue-se o mesmo padrão, mas ao contrário:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()`é chamado a cada ouvinte.
    - O sinal de cancelamento `runAsync()` que foi passado está cancelado. Verificando `isCancelled` as devoluções `true`de propriedade do token de cancelamento, e se for chamado, o método do `throwIfCancellationRequested` token lança um `CancellationException`.
2. Quando `closeAsync()` termina em cada `runAsync()` ouvinte e também termina, `StatelessService.onCloseAsync()` o método do serviço é chamado, se estiver presente. Mais uma vez, esta não é uma sobreposição comum, mas pode ser usada para fechar recursos com segurança, parar o processamento de antecedentes, terminar a poupança de estado externo ou encerrar as ligações existentes.
3. Após `StatelessService.onCloseAsync()` os acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-startup"></a>Startup de serviço sinuoso
Os serviços estatais têm um padrão semelhante aos serviços apátridas, com algumas mudanças.  Aqui está a ordem dos eventos para iniciar um serviço imponente:

1. O serviço é construído.
2. `StatefulServiceBase.onOpenAsync()`é chamado. Esta chamada não é geralmente ultrapassada no serviço.
3. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()`é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes devolvidos estão abertos. `CommunicationListener.openAsync()`é chamado a cada ouvinte.
      - Se o serviço for um serviço secundário, apenas os ouvintes marcados como `listenOnSecondary = true` estão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for atualmente primário, `StatefulServiceBase.runAsync()` o método do serviço é chamado.
4. Depois de todas as `openAsync()` chamadas do `runAsync()` ouvinte `StatefulServiceBase.onChangeRoleAsync()` da réplica terminarem e é chamada, é chamada. Esta chamada não é geralmente ultrapassada no serviço.

À semelhança dos serviços apátridas, em serviço sinuoso, não há `runAsync` coordenação entre a ordem em que os ouvintes são criados e abertos e quando é chamado. Se precisar de coordenação, as soluções são as mesmas. Mas há um caso adicional para o serviço de estado. Diga que as chamadas que chegam aos ouvintes de comunicação requerem informações mantidas dentro de [algumas Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) Como os ouvintes de comunicação podem abrir antes que `runAsync` as Coleções Fiáveis sejam legíveis ou relegáveis, e antes de começar, é necessária alguma coordenação adicional. A solução mais simples e comum é que os ouvintes de comunicação devolvam um código de erro. O cliente usa o código de erro para saber para voltar a tentar o pedido.

## <a name="stateful-service-shutdown"></a>Paralisação de serviços audais
Tal como os serviços apátridas, os eventos de ciclo de vida durante o encerramento são os mesmos que durante o arranque, mas invertidos. Quando um serviço imponente está a ser encerrado, ocorrem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()`é chamado a cada ouvinte.
    - O sinal de cancelamento `runAsync()` que foi passado está cancelado. Uma chamada para o método `isCancelled()` do `true`token de cancelamento retorna `throwIfCancellationRequested()` , e `OperationCanceledException`se for chamado, o método do token lança um .
2. Depois `closeAsync()` de terminar em `runAsync()` cada ouvinte e também `StatefulServiceBase.onChangeRoleAsync()` termina, o serviço é chamado. Esta chamada não é geralmente ultrapassada no serviço.

   > [!NOTE]  
   > Esperar `runAsync` para terminar só é necessário se esta réplica for uma réplica primária.

3. Após `StatefulServiceBase.onChangeRoleAsync()` o fim do `StatefulServiceBase.onCloseAsync()` método, o método é chamado. Esta chamada é uma sobreposição incomum, mas está disponível.
3. Após `StatefulServiceBase.onCloseAsync()` os acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviço sinuosos
Enquanto um serviço imponente está em execução, os ouvintes de comunicação são abertos e o `runAsync` método é chamado apenas para as réplicas primárias desses serviços estatais. Réplicas secundárias são construídas, mas não vêem mais chamadas. Enquanto um serviço imponente está em execução, a réplica que é atualmente a primária pode mudar. Os eventos de ciclo de vida que uma réplica estatal pode ver depende se é a réplica sendo despromovida ou promovida durante a troca.

### <a name="for-the-demoted-primary"></a>Para as primárias despromovidas
O Serviço Fabric precisa da réplica primária que é despromovida para parar de processar mensagens e parar qualquer trabalho de fundo. Este passo é semelhante ao de quando o serviço é desligado. Uma diferença é que o serviço não está destruído ou fechado, porque permanece como secundário. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()`é chamado a cada ouvinte.
    - O sinal de cancelamento `runAsync()` que foi passado está cancelado. Uma verificação do método `isCancelled()` do `true`token de cancelamento retorna. Se for chamado, o `throwIfCancellationRequested()` método do `OperationCanceledException`token lança um .
2. Depois `closeAsync()` de terminar em `runAsync()` cada ouvinte e também `StatefulServiceBase.onChangeRoleAsync()` termina, o serviço é chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="for-the-promoted-secondary"></a>Para o promovido secundário
Da mesma forma, o Service Fabric precisa da réplica secundária que é promovida para começar a ouvir mensagens no fio, e para iniciar quaisquer tarefas de fundo que precise de completar. Este processo é semelhante ao momento em que o serviço é criado. A diferença é que a réplica em si já existe. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()`é invocado e quaisquer ouvintes devolvidos são abertos. `CommunicationListener.openAsync()`é chamado a cada ouvinte.
    - O método `StatefulServiceBase.runAsync()` do serviço é chamado.
2. Depois de todas as `openAsync()` chamadas do `runAsync()` ouvinte `StatefulServiceBase.onChangeRoleAsync()` da réplica terminarem e é chamada, é chamada. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Questões comuns durante a paragem do serviço e despromoção primária
O Tecido de Serviço altera a primária de um serviço de estado por múltiplas razões. As razões mais comuns são o [reequilíbrio](service-fabric-cluster-resource-manager-balancing.md) do cluster e [a atualização da aplicação.](service-fabric-application-upgrade.md) Durante estas operações, é importante que `cancellationToken`o serviço respeite o . Isto também se aplica durante a paragem normal do serviço, como se o serviço tivesse sido apagado.

Serviços que não lidam com o cancelamento de forma limpa podem experimentar vários problemas. Estas operações são lentas porque o Service Fabric espera que os serviços parem graciosamente. Isto pode, em última análise, levar a atualizações falhadas que o tempo de saída e a reversão. A não honração do símbolo de cancelamento também pode causar aglomerados desequilibrados. Os aglomerados ficam desequilibrados porque os nós ficam quentes. No entanto, os serviços não podem ser reequilibrados porque demora muito tempo a movê-los para outro lugar. 

Como os serviços são imponentes, também é provável que os serviços utilizem [Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) No Tecido de Serviço, quando uma primária é despromovida, uma das primeiras coisas que acontece é que o acesso por escrito ao estado subjacente é revogado. Isto leva a um segundo conjunto de questões que podem afetar o ciclo de vida do serviço. As coleções devolvem exceções com base no tempo e se a réplica está a ser movida ou encerrada. É importante lidar com estas exceções corretamente. 

As exceções lançadas pelo Tecido de Serviço são [permanentes`FabricException`](https://docs.microsoft.com/java/api/system.fabric.exception) ou transitórias [.`FabricTransientException`](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception) As exceções permanentes devem ser registadas e lançadas. Exceções transitórias podem ser novamente tentadas com base na lógica de retry.

Uma parte importante do teste e validação de Serviços `ReliableCollections` Fiáveis é lidar com as exceções que advêm da utilização do em conjunto com eventos de ciclo de vida de serviço. Recomendamos que execute sempre o seu serviço sob carga. Também deve realizar upgrades e testes de [caos](service-fabric-controlled-chaos.md) antes de ser lançado para produção. Estes passos básicos ajudam a garantir que o seu serviço é implementado corretamente e que lida corretamente com eventos de ciclo de vida.

## <a name="notes-on-service-lifecycle"></a>Notas sobre o ciclo de vida do serviço
* Tanto `runAsync()` o método `createServiceInstanceListeners/createServiceReplicaListeners` como as chamadas são opcionais. Um serviço pode ter um, ambos, ou nenhum dos dois. Por exemplo, se o serviço fizer todo o seu trabalho em resposta `runAsync()`às chamadas dos utilizadores, não há necessidade de implementá-lo . Apenas os ouvintes de comunicação e o seu código associado são necessários.  Da mesma forma, criar e devolver ouvintes de comunicação é opcional. O serviço pode ter apenas trabalho de fundo `runAsync()`para fazer, pelo que só precisa de implementar.
* É válido para um serviço `runAsync()` para completar com sucesso e voltar dele. Isto não é considerado uma condição de falha. Representa o trabalho de fundo do acabamento de serviço. Para serviços fiáveis `runAsync()` audais, seria novamente chamado se o serviço fosse despromovido das primárias, e depois promovido de volta às primárias.
* Se um serviço `runAsync()` sair de um serviço lançando alguma exceção inesperada, isto é um fracasso. O objeto de serviço está desligado e um erro de saúde é reportado.
* Embora não haja um limite de tempo para voltar a partir destes métodos, você imediatamente perde a capacidade de escrever. Portanto, não pode completar nenhum trabalho real. Recomendamos que regresse o mais rápido possível ao receber o pedido de cancelamento. Se o seu serviço não responder a estas chamadas API num período razoável de tempo, o Service Fabric poderá rescindir à força o seu serviço. Normalmente, isto só acontece durante as atualizações da aplicação ou quando um serviço está a ser apagado. Este intervalo é de 15 minutos por defeito.
* Falhas no `onCloseAsync()` caminho resultam em `onAbort()` ser chamados. Esta chamada é uma última oportunidade, melhor esforço para o serviço limpar e libertar todos os recursos que eles reivindicaram. Isto é geralmente chamado quando uma falha permanente é detetada no nó, ou quando o Tecido de Serviço não pode gerir de forma fiável o ciclo de vida da instância de serviço devido a falhas internas.
* `OnChangeRoleAsync()`é chamada quando a réplica do serviço está a mudar de papel, por exemplo, para primária ou secundária. As réplicas primárias recebem o estatuto de escrita (são autorizadas a criar e escrever para Coleções Fiáveis). As réplicas secundárias recebem o estatuto de leitura (só podem ser lidas a partir das coleções fiáveis existentes). A maioria dos trabalhos num serviço de estado é realizado na réplica primária. As réplicas secundárias podem realizar validação apenas de leitura, geração de relatórios, mineração de dados ou outros trabalhos apenas de leitura.

## <a name="next-steps"></a>Passos seguintes
* [Introdução a Serviços Fiáveis](service-fabric-reliable-services-introduction.md)
* [Serviços fiáveis arranque rápido](service-fabric-reliable-services-quick-start-java.md)

