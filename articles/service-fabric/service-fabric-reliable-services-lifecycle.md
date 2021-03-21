---
title: Visão geral do ciclo de vida dos Serviços Fiáveis
description: Conheça os eventos do ciclo de vida numa aplicação Azure Service Reliable Services para serviços stateful e apátridas.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784601"
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do ciclo de vida dos serviços fiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando se pensa nos ciclos de vida da Azure Service Fabric Reliable Services, o básico do ciclo de vida é o mais importante. Em geral, o ciclo de vida inclui o seguinte:

- Durante o arranque:
  - Os serviços são construídos.
  - Os serviços têm a oportunidade de construir e devolver zero ou mais ouvintes.
  - Todos os ouvintes devolvidos estão abertos, permitindo a comunicação com o serviço.
  - O método **RunAsync** do serviço é chamado, permitindo que o serviço faça tarefas de longa duração ou trabalho de fundo.
- Durante o encerramento:
  - O sinal de cancelamento passado para **RunAsync** está cancelado, e os ouvintes estão fechados.
  - Após o encerramento dos ouvintes, o próprio objeto de serviço é destruído.

Há detalhes em torno da ordem exata destes eventos. A ordem dos eventos pode mudar ligeiramente dependendo se o Serviço Fiável é apátrida ou stateful. Além disso, para os serviços estatais, temos de lidar com o cenário de troca primária. Durante esta sequência, o papel de Primário é transferido para outra réplica (ou volta) sem que o serviço seja desligado. Por último, temos de pensar em erros ou em condições de falha.

## <a name="stateless-service-startup"></a>Startup de serviço apátrida
O ciclo de vida de um serviço apátrida é simples. Aqui está a ordem dos acontecimentos:

1. O serviço é construído.
2. Depois, paralelamente, acontecem duas coisas:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e quaisquer ouvintes devolvidos são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método do serviço `StatelessService.RunAsync()` chama-se.
3. Se estiver presente, o método do serviço `StatelessService.OnOpenAsync()` é chamado. Esta chamada é uma sobreposição incomum, mas está disponível. As tarefas de inicialização de serviço alargadas podem ser iniciadas neste momento.

Tenha em mente que não há ordem entre as chamadas para criar e abrir os ouvintes e **RunAsync**. Os ouvintes podem abrir antes **do RunAsync** começar. Da mesma forma, pode invocar **o RunAsync** antes que os ouvintes da comunicação estejam abertos ou mesmo construídos. Se for necessária sincronização, é deixado como exercício ao implementador. Aqui estão algumas soluções comuns:

  - Às vezes, os ouvintes não podem funcionar até que alguma outra informação seja criada ou o trabalho seja feito. Para os serviços apátridas, esse trabalho pode geralmente ser feito noutros locais, tais como: 
    - No construtor do serviço.
    - Durante a `CreateServiceInstanceListeners()` chamada.
    - Como parte da construção do próprio ouvinte.
  - Às vezes, o código em **RunAsync** não começa até os ouvintes estarem abertos. Neste caso, é necessária uma coordenação adicional. Uma solução comum é que há uma bandeira dentro dos ouvintes que indica quando terminam. Esta bandeira é então verificada em **RunAsync** antes de continuar a trabalhar real.

## <a name="stateless-service-shutdown"></a>Encerramento de serviço apátrida
Para encerrar um serviço apátrida, segue-se o mesmo padrão, apenas ao contrário:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento passado `RunAsync()` é cancelado. Uma verificação da propriedade do token de cancelamento `IsCancellationRequested` retorna verdadeira, e se for chamada, o método do token `ThrowIfCancellationRequested` lança um `OperationCanceledException` .
2. Depois de `CloseAsync()` terminar em cada ouvinte e também `RunAsync()` terminar, o método do serviço é `StatelessService.OnCloseAsync()` chamado, se presente.  O OnCloseAsync é chamado quando a instância de serviço apátrida vai ser graciosamente encerrada. Isto pode ocorrer quando o código do serviço está a ser atualizado, a instância de serviço está a ser movida devido ao equilíbrio de carga, ou é detetada uma falha transitória. É incomum `StatelessService.OnCloseAsync()` sobrepor-se, mas pode ser usado para fechar recursos com segurança, parar o processamento de antecedentes, terminar a poupança de estado externo ou fechar as ligações existentes.
3. Após `StatelessService.OnCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-startup"></a>Startup de serviço stateful
Os serviços estatais têm um padrão semelhante aos serviços apátridas, com algumas mudanças. Para iniciar um serviço estatal, a ordem dos eventos é a seguinte:

1. O serviço é construído.
2. `StatefulServiceBase.OnOpenAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.
3. As seguintes coisas acontecem em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes devolvidos são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
      - Se o serviço for um serviço secundário, apenas os ouvintes marcados como `ListenOnSecondary = true` estão abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for atualmente um Primário, o método do serviço `StatefulServiceBase.RunAsync()` é chamado.
4. Depois de todas as chamadas do ouvinte da réplica `OpenAsync()` terminarem e `RunAsync()` são chamadas, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

À semelhança dos serviços apátridas, não há coordenação entre a ordem pela qual os ouvintes são criados e abertos e quando **o RunAsync** é chamado. Se precisa de coordenação, as soluções são as mesmas. Há um caso adicional para o serviço estatal. Diga que as chamadas que chegam aos ouvintes de comunicação requerem informações guardadas dentro [de algumas Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md)

   > [!NOTE]  
   > Como os ouvintes de comunicação podem abrir antes que as coleções fiáveis sejam legíveis ou escritas, e antes **que o RunAsync** possa começar, é necessária alguma coordenação adicional. A solução mais simples e comum é que os ouvintes de comunicação devolvam um código de erro que o cliente usa para saber para voltar a tentar o pedido.

## <a name="stateful-service-shutdown"></a>Encerramento de serviços imponentes
Tal como os serviços apátridas, os eventos do ciclo de vida durante a paralisação são os mesmos que durante o arranque, mas invertidos. Quando um serviço estatal está a ser encerrado, ocorrem os seguintes eventos:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento passado `RunAsync()` é cancelado. Uma verificação da propriedade do token de cancelamento `IsCancellationRequested` retorna verdadeira, e se for chamada, o método do token `ThrowIfCancellationRequested` lança um `OperationCanceledException` .
2. Depois de `CloseAsync()` terminar em cada ouvinte e também `RunAsync()` terminar, o serviço é `StatefulServiceBase.OnChangeRoleAsync()` chamado. Esta chamada não é geralmente ultrapassada no serviço.

   > [!NOTE]  
   > A necessidade de esperar que **o RunAsync** termine só é necessária se esta réplica for uma réplica primária.

3. Após o fim do `StatefulServiceBase.OnChangeRoleAsync()` método, o `StatefulServiceBase.OnCloseAsync()` método é chamado. Esta chamada é uma sobreposição incomum, mas está disponível.
3. Após `StatefulServiceBase.OnCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviços estatais
Enquanto um serviço estatal está em execução, apenas as réplicas primárias desse estado têm os seus ouvintes de comunicação abertos e o seu método **RunAsync** chamado. Réplicas secundárias são construídas, mas não vêem mais chamadas. Enquanto um serviço estatal está em execução, a réplica que é atualmente a Primária pode mudar como resultado da falha ou da otimização do equilíbrio do cluster. O que isto significa em termos dos eventos do ciclo de vida que uma réplica pode ver? O comportamento que a réplica imponente vê depende se é a réplica a ser despromovida ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para as Primárias que é despromovido
Para a réplica primária que é despromostes, o Service Fabric precisa desta réplica para parar de processar mensagens e desistir de qualquer trabalho de fundo que esteja a fazer. Como resultado, este passo parece que foi quando o serviço é encerrado. Uma diferença é que o serviço não é destruído ou fechado porque permanece como secundário. As seguintes APIs são chamadas:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado em cada ouvinte.
    - O sinal de cancelamento passado `RunAsync()` é cancelado. Uma verificação da propriedade do token de cancelamento `IsCancellationRequested` retorna verdadeira, e se for chamada, o método do token `ThrowIfCancellationRequested` lança um `OperationCanceledException` .
2. Depois de `CloseAsync()` terminar em cada ouvinte e também `RunAsync()` terminar, o serviço é `StatefulServiceBase.OnChangeRoleAsync()` chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para o Secundário que é promovido
Da mesma forma, o Service Fabric precisa da réplica secundária que é promovida para começar a ouvir mensagens no fio e iniciar quaisquer tarefas de fundo que precise de completar. Como resultado, este processo parece que foi quando o serviço é criado, exceto que a réplica em si já existe. As seguintes APIs são chamadas:

1. Paralelamente:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e quaisquer ouvintes devolvidos são abertos. `ICommunicationListener.OpenAsync()` é chamado em cada ouvinte.
    - O método do serviço `StatefulServiceBase.RunAsync()` chama-se.
2. Depois de todas as chamadas do ouvinte da réplica `OpenAsync()` terminarem e `RunAsync()` são chamadas, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Questões comuns durante o encerramento de serviços e despromoção primário
O Tecido de Serviço altera a Primária de um serviço estatal por uma variedade de razões. Os mais comuns são [o reequilíbrio do cluster](service-fabric-cluster-resource-manager-balancing.md) e a [atualização da aplicação.](service-fabric-application-upgrade.md) Durante estas operações (bem como durante a paragem normal do serviço, como se veria se o serviço foi apagado), é importante que o serviço respeite o `CancellationToken` . 

Os serviços que não lidam com o cancelamento de forma limpa podem experimentar vários problemas. Estas operações são lentas porque a Service Fabric espera que os serviços parem graciosamente. Isto pode, em última análise, levar a melhorias falhadas que o tempo para fora e para trás. A falta de honrar o símbolo de cancelamento também pode causar aglomerados desequilibrados. Os aglomerados tornam-se desequilibrados porque os nós ficam quentes, mas os serviços não podem ser reequilibrados porque demora muito tempo a movê-los para outro lugar. 

Como os serviços são imponentes, também é provável que utilizem as [Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) No Tecido de Serviço, quando uma Primária é despromovada, uma das primeiras coisas que acontece é que escrever acesso ao estado subjacente é revogado. Isto leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. As coleções devolvem exceções com base no tempo e se a réplica está a ser movida ou desligada. Estas exceções devem ser manuseadas corretamente. As exceções lançadas pelo Tecido de Serviço enquadram-se em categorias permanentes [ `FabricException` ()](/dotnet/api/system.fabric.fabricexception) e transitórias. [ `FabricTransientException` ](/dotnet/api/system.fabric.fabrictransientexception) As exceções permanentes devem ser registadas e lançadas, enquanto as exceções transitórias podem ser novamente julgadas com base em alguma lógica de relemissão.

Lidar com as exceções que vêm da utilização do `ReliableCollections` em conjunto com eventos de ciclo de vida de serviço é uma parte importante do teste e validação de um Serviço Fiável. Recomendamos que utilize sempre o seu serviço com carga enquanto realiza upgrades e [testes de caos](service-fabric-controlled-chaos.md) antes de se deslocar para a produção. Estes passos básicos ajudam a garantir que o seu serviço é corretamente implementado e lida corretamente com os eventos do ciclo de vida.


## <a name="notes-on-the-service-lifecycle"></a>Notas sobre o ciclo de vida de serviço
  - Tanto o método como `RunAsync()` as `CreateServiceReplicaListeners/CreateServiceInstanceListeners` chamadas são opcionais. Um serviço pode ter um deles, ambos, ou nenhum dos dois. Por exemplo, se o serviço fizer todo o seu trabalho em resposta às chamadas dos utilizadores, não há necessidade de implementar `RunAsync()` . Apenas os ouvintes de comunicação e o seu código associado são necessários. Da mesma forma, criar e devolver ouvintes de comunicação é opcional, uma vez que o serviço só pode ter trabalho de fundo para fazer, pelo que só precisa de `RunAsync()` implementar.
  - É válido para um serviço para completar `RunAsync()` com sucesso e regressar dele. Completar não é uma condição de falha. A conclusão `RunAsync()` indica que o trabalho de fundo do serviço terminou. Para serviços fidedigtos e imponentes, `RunAsync()` é chamado novamente se a réplica for despromovida de Primária para Secundária e depois promovida de volta para a Primária.
  - Se um serviço `RunAsync()` sair, lançando alguma exceção inesperada, isto constitui uma falha. O objeto de serviço é desligado e um erro de saúde é reportado.
  - Embora não exista um limite de tempo para regressar destes métodos, perde-se imediatamente a capacidade de escrever para a Reliable Collections e, portanto, não pode concluir nenhum trabalho real. Recomendamos que regresse o mais rápido possível ao receber o pedido de cancelamento. Se o seu serviço não responder a estas chamadas API num período de tempo razoável, a Service Fabric pode encerrar à força o seu serviço. Normalmente isto só acontece durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por defeito.
  - Falhas no `OnCloseAsync()` caminho resultam na `OnAbort()` chamada, que é uma oportunidade de última oportunidade para o serviço limpar e libertar quaisquer recursos que tenham reclamado. Isto é geralmente chamado quando uma falha permanente é detetada no nó, ou quando o Tecido de Serviço não consegue gerir de forma fiável o ciclo de vida da instância de serviço devido a falhas internas.
  - `OnChangeRoleAsync()` é chamada quando a réplica de serviço imponente está mudando de papel, por exemplo para primária ou secundária. Réplicas primárias recebem o estado de escrita (são permitidas a criar e escrever para Coleções Fiáveis). As réplicas secundárias recebem o estado de leitura (só podem ler-se das coleções fiáveis existentes). A maioria dos trabalhos num serviço estatal é realizada na réplica primária. Réplicas secundárias podem realizar validação apenas de leitura, geração de relatórios, mineração de dados ou outros trabalhos apenas de leitura.

## <a name="next-steps"></a>Passos seguintes
- [Introdução a Serviços Fiáveis](service-fabric-reliable-services-introduction.md)
- [Início rápido dos Serviços Fiáveis](service-fabric-reliable-services-quick-start.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
