---
title: Ciclo de vida de serviços fiáveis de tecido de serviços Azure
description: Conheça os eventos do ciclo de vida numa aplicação Azure Service Fabric Reliable Services utilizando a Java para serviços imponentes e apátridas.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324612"
---
# <a name="reliable-services-lifecycle"></a>Ciclo de vida dos Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

A Reliable Services é um dos modelos de programação disponíveis no Azure Service Fabric. Ao aprender sobre o ciclo de vida dos Serviços Fiáveis, é mais importante compreender os eventos básicos do ciclo de vida. A encomenda exata dos eventos depende dos detalhes da configuração. 

Em geral, o ciclo de vida dos Serviços Fiáveis inclui os seguintes eventos:

* Durante o arranque:
  * Os serviços são construídos.
  * Os serviços têm a oportunidade de construir e devolver zero ou mais ouvintes.
  * Todos os ouvintes devolvidos estão abertos, para comunicação com o serviço.
  * O método do serviço `runAsync` é chamado, para que o serviço possa fazer trabalhos de longa duração ou de fundo.
* Durante o encerramento:
  * O sinal de cancelamento que foi passado `runAsync` é cancelado, e os ouvintes estão fechados.
  * O objeto de serviço em si está destruído.

A ordem dos eventos em Serviços Fiáveis pode mudar ligeiramente dependendo se o serviço fiável é apátrida ou stateful. 

Além disso, para serviços estatais, deve abordar o cenário de troca primária. Durante esta sequência, o papel primário é transferido para outra réplica (ou volta) sem que o serviço seja desligado. 

Finalmente, tem que pensar em erros ou condições de falha.

## <a name="stateless-service-startup"></a>Startup de serviço apátrida
O ciclo de vida de um serviço apátrida é bastante simples. Aqui está a ordem dos acontecimentos:

1. O serviço é construído.
2. Estes eventos ocorrem em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado, e quaisquer ouvintes devolvidos são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método do serviço `runAsync` `StatelessService.runAsync()` é chamado.
3. Se estiver presente, o próprio método do serviço `onOpenAsync` é chamado. Especificamente, `StatelessService.onOpenAsync()` chama-se. Esta é uma sobreposição incomum, mas está disponível.

É importante notar que não há ordem entre a chamada para criar e abrir os ouvintes e a chamada para `runAsync` . Os ouvintes podem abrir antes `runAsync` de começar. Da mesma forma, `runAsync` pode ser invocado antes que os ouvintes da comunicação estejam abertos, ou antes mesmo de terem sido construídos. Se for necessária sincronização, deve ser feita pelo implementador. Aqui estão algumas soluções comuns:

* Às vezes, os ouvintes não podem funcionar até que outras informações são criadas ou outro trabalho é feito. Para os serviços apátridas, esse trabalho geralmente pode ser feito no construtor do serviço. Pode ser feito durante a `createServiceInstanceListeners()` chamada, ou como parte da construção do próprio ouvinte.
* Às vezes, o código `runAsync` não começa até os ouvintes estarem abertos. Neste caso, é necessária uma coordenação adicional. Uma solução comum é adicionar uma bandeira aos ouvintes. A bandeira indica quando os ouvintes terminaram. O `runAsync` método verifica isto antes de continuar o trabalho real.

## <a name="stateless-service-shutdown"></a>Encerramento de serviço apátrida
Ao encerrar um serviço apátrida, segue-se o mesmo padrão, mas ao contrário:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento que foi passado `runAsync()` para ser cancelado. Verificando as devoluções de bens do token de cancelamento `isCancelled` , e se for `true` chamado, o método do token `throwIfCancellationRequested` lança um `CancellationException` .
2. Quando `closeAsync()` termina em cada ouvinte e também `runAsync()` termina, o método do serviço é `StatelessService.onCloseAsync()` chamado, se estiver presente. Mais uma vez, esta não é uma sobreposição comum, mas pode ser usada para fechar com segurança recursos, parar o processamento de antecedentes, terminar de salvar o estado externo, ou fechar as ligações existentes.
3. Após `StatelessService.onCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-startup"></a>Startup de serviço stateful
Os serviços estatais têm um padrão semelhante aos serviços apátridas, com algumas alterações.  Aqui está a ordem dos eventos para iniciar um serviço estatal:

1. O serviço é construído.
2. `StatefulServiceBase.onOpenAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.
3. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes devolvidos são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, apenas os ouvintes `listenOnSecondary = true` assinalam-se como estão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for atualmente primário, o método do serviço `StatefulServiceBase.runAsync()` é chamado.
4. Depois de todas as chamadas do ouvinte da réplica `openAsync()` terminarem e `runAsync()` são chamadas, `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

À semelhança dos serviços apátridas, em serviço estatal, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando `runAsync` são chamados. Se precisa de coordenação, as soluções são as mesmas. Mas há um caso adicional para o serviço estatal. Diga que as chamadas que chegam aos ouvintes de comunicação requerem informações guardadas dentro [de algumas Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) Porque os ouvintes de comunicação podem abrir antes que as Coleções Fiáveis sejam legíveis ou legívels, e antes `runAsync` de começar, é necessária alguma coordenação adicional. A solução mais simples e comum é que os ouvintes de comunicação devolvam um código de erro. O cliente utiliza o código de erro para saber voltar a tentar o pedido.

## <a name="stateful-service-shutdown"></a>Encerramento de serviços imponentes
Tal como os serviços apátridas, os eventos do ciclo de vida durante a paralisação são os mesmos que durante o arranque, mas invertidos. Quando um serviço estatal está a ser encerrado, ocorrem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento que foi passado `runAsync()` para ser cancelado. Uma chamada para o método do token de cancelamento `isCancelled()` retorna `true` , e se chamado, o método do token `throwIfCancellationRequested()` lança um `OperationCanceledException` .
2. Depois de `closeAsync()` terminar em cada ouvinte e também `runAsync()` terminar, o serviço é `StatefulServiceBase.onChangeRoleAsync()` chamado. Esta chamada não é geralmente ultrapassada no serviço.

   > [!NOTE]  
   > Esperar `runAsync` que termine só é necessário se esta réplica for uma réplica primária.

3. Após o fim do `StatefulServiceBase.onChangeRoleAsync()` método, o `StatefulServiceBase.onCloseAsync()` método é chamado. Esta chamada é uma sobreposição incomum, mas está disponível.
3. Após `StatefulServiceBase.onCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviços estatais
Enquanto um serviço estatal está em execução, os ouvintes de comunicação são abertos e o `runAsync` método é chamado apenas para as réplicas primárias desse estado serviços. Réplicas secundárias são construídas, mas não vêem mais chamadas. Enquanto um serviço estatal está em execução, a réplica que é atualmente a principal pode mudar. Os eventos do ciclo de vida que uma réplica imponente pode ver depende se é a réplica a ser despromovida ou promovida durante a troca.

### <a name="for-the-demoted-primary"></a>Para as primárias despromodas
O Tecido de Serviço precisa da réplica primária que é despromovada para parar o processamento de mensagens e parar qualquer trabalho de fundo. Este passo é semelhante ao de quando o serviço é desligado. Uma diferença é que o serviço não é destruído ou fechado, porque permanece como secundário. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Todos os ouvintes abertos estão fechados. `CommunicationListener.closeAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento que foi passado `runAsync()` para ser cancelado. Uma verificação do método do token de cancelamento `isCancelled()` devolve `true` . Se for chamado, o método do símbolo `throwIfCancellationRequested()` atira um `OperationCanceledException` .
2. Depois de `closeAsync()` terminar em cada ouvinte e também `runAsync()` terminar, o serviço é `StatefulServiceBase.onChangeRoleAsync()` chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="for-the-promoted-secondary"></a>Para o secundário promovido
Da mesma forma, o Service Fabric precisa da réplica secundária que é promovida para começar a ouvir mensagens no fio, e para iniciar quaisquer tarefas de fundo que precise de completar. Este processo é semelhante ao de quando o serviço é criado. A diferença é que a réplica em si já existe. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado e quaisquer ouvintes devolvidos são abertos. `CommunicationListener.openAsync()` é chamado em cada ouvinte.
    - O método do serviço `StatefulServiceBase.runAsync()` chama-se.
2. Depois de todas as chamadas do ouvinte da réplica `openAsync()` terminarem e `runAsync()` são chamadas, `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Questões comuns durante o encerramento de serviços e despromoção primário
O Tecido de Serviço altera a primária de um serviço estatal por múltiplas razões. As razões mais comuns são [o reequilíbrio do cluster](service-fabric-cluster-resource-manager-balancing.md) e a [atualização da aplicação.](service-fabric-application-upgrade.md) Durante estas operações, é importante que o serviço respeite o `cancellationToken` . Isto também se aplica durante a paragem normal do serviço, como se o serviço fosse eliminado.

Os serviços que não lidam com o cancelamento de forma limpa podem experimentar vários problemas. Estas operações são lentas porque a Service Fabric espera que os serviços parem graciosamente. Isto pode, em última análise, levar a melhorias falhadas que o tempo para fora e o reversão. A falta de honrar o token de cancelamento também pode causar aglomerados desequilibrados. Os aglomerados tornam-se desequilibrados porque os nós ficam quentes. No entanto, os serviços não podem ser reequilibridos porque demora muito tempo a mudá-los para outro lugar. 

Como os serviços são imponentes, também é provável que os serviços utilizem [Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) No Tecido de Serviço, quando uma primária é despromovada, uma das primeiras coisas que acontece é que escrever acesso ao estado subjacente é revogado. Isto leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. As coleções devolvem exceções com base no tempo e se a réplica está a ser movida ou desligada. É importante lidar com estas exceções corretamente. 

As exceções lançadas pela Service Fabric são permanentes [ `FabricException` ()](/java/api/system.fabric.exception) ou [transitórias ( `FabricTransientException` )](/java/api/system.fabric.exception.fabrictransientexception). As exceções permanentes devem ser registadas e lançadas. As exceções transitórias podem ser novamente julgadas com base na lógica de retíria.

Uma parte importante do teste e validação de Serviços Fiáveis é lidar com as exceções que vêm da utilização em conjunto com eventos de ciclo de vida de `ReliableCollections` serviço. Recomendamos que utilize sempre o seu serviço com carga. Também deve realizar upgrades e [testes de caos](service-fabric-controlled-chaos.md) antes de ser implantado na produção. Estes passos básicos ajudam a garantir que o seu serviço é implementado corretamente e que lida corretamente com os eventos do ciclo de vida.

## <a name="notes-on-service-lifecycle"></a>Notas sobre o ciclo de vida de serviço
* Tanto o método como `runAsync()` as `createServiceInstanceListeners/createServiceReplicaListeners` chamadas são opcionais. Um serviço pode ter um, ambos, ou nenhum dos dois. Por exemplo, se o serviço fizer todo o seu trabalho em resposta às chamadas dos utilizadores, não há necessidade de implementar `runAsync()` . Apenas os ouvintes de comunicação e o seu código associado são necessários.  Da mesma forma, criar e devolver ouvintes de comunicação é opcional. O serviço pode ter apenas trabalho de fundo para fazer, por isso só precisa de `runAsync()` implementar.
* É válido para um serviço para completar `runAsync()` com sucesso e voltar dele. Isto não é considerado uma condição de fracasso. Representa o trabalho de fundo do acabamento do serviço. Para serviços fidedigdos, `runAsync()` seria chamado novamente se o serviço fosse despromovido das primárias, e depois promovido de volta às primárias.
* Se um serviço `runAsync()` sair, lançando uma exceção inesperada, isto é um fracasso. O objeto de serviço está desligado e é reportado um erro de saúde.
* Embora não haja um limite de tempo para regressar destes métodos, perde-se imediatamente a capacidade de escrever. Portanto, não se pode completar nenhum trabalho a sério. Recomendamos que regresse o mais rápido possível ao receber o pedido de cancelamento. Se o seu serviço não responder a estas chamadas API num período de tempo razoável, a Service Fabric poderá encerrar à força o seu serviço. Normalmente, isto acontece apenas durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por defeito.
* Falhas no `onCloseAsync()` caminho resultam em `onAbort()` ser chamado. Esta chamada é uma última oportunidade, a melhor oportunidade de esforço para o serviço limpar e libertar todos os recursos que eles reivindicaram. Isto é geralmente chamado quando uma falha permanente é detetada no nó, ou quando o Tecido de Serviço não consegue gerir de forma fiável o ciclo de vida da instância de serviço devido a falhas internas.
* `OnChangeRoleAsync()` é chamada quando a réplica de serviço imponente está mudando de papel, por exemplo para primária ou secundária. Réplicas primárias recebem o estado de escrita (são permitidas a criar e escrever para Coleções Fiáveis). As réplicas secundárias recebem o estado de leitura (só podem ler-se das coleções fiáveis existentes). A maioria dos trabalhos num serviço estatal é realizada na réplica primária. Réplicas secundárias podem realizar validação apenas de leitura, geração de relatórios, mineração de dados ou outros trabalhos apenas de leitura.

## <a name="next-steps"></a>Passos seguintes
* [Introdução a Serviços Fiáveis](service-fabric-reliable-services-introduction.md)
* [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start-java.md)
