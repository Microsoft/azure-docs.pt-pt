---
title: Visão geral do ciclo de vida dos Serviços Fiáveis
description: Conheça os eventos de ciclo de vida numa aplicação azure service Fabric Reliable Services para serviços apátridas e apátridas.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391359"
---
# <a name="reliable-services-lifecycle-overview"></a>Visão geral do ciclo de vida dos Serviços Fiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando se está a pensar nos ciclos de vida dos Serviços Fiáveis do Tecido de Serviço Azure, os fundamentos do ciclo de vida são os mais importantes. Em geral, o ciclo de vida inclui o seguinte:

- Durante o arranque:
  - Os serviços são construídos.
  - Os serviços têm a oportunidade de construir e devolver zero ou mais ouvintes.
  - Todos os ouvintes devolvidos são abertos, permitindo a comunicação com o serviço.
  - O método **RunAsync** do serviço é chamado, permitindo que o serviço faça tarefas de longo prazo ou trabalho de fundo.
- Durante a paragem:
  - O sinal de cancelamento passado para **RunAsync** está cancelado, e os ouvintes estão fechados.
  - Após o encerramento dos ouvintes, o objeto de serviço em si é destruído.

Há detalhes em torno da ordem exata destes eventos. A ordem dos eventos pode mudar ligeiramente dependendo se o Serviço Fiável é apátrida ou apátrida. Além disso, para serviços estatais, temos de lidar com o cenário de troca primária. Durante esta sequência, o papel da Primary é transferido para outra réplica (ou volta) sem que o serviço seja desligado. Por último, temos de pensar em condições de erro ou de falha.

## <a name="stateless-service-startup"></a>Startup de serviço apátrida
O ciclo de vida de um serviço apátrida é simples. Aqui está a ordem dos acontecimentos:

1. O serviço é construído.
2. Então, em paralelo, duas coisas acontecem:
    - `StatelessService.CreateServiceInstanceListeners()` é invocado e todos os ouvintes devolvidos são abertos. `ICommunicationListener.OpenAsync()` é chamado a cada ouvinte.
    - O método de `StatelessService.RunAsync()` do serviço é chamado.
3. Se presente, o método de `StatelessService.OnOpenAsync()` do serviço é chamado. Esta chamada é uma sobreposição incomum, mas está disponível. Neste momento, podem ser iniciadas tarefas de inicialização de serviço supor.

Tenha em mente que não há ordem entre as chamadas para criar e abrir os ouvintes e **RunAsync**. Os ouvintes podem abrir antes **do RunAsync** ser iniciado. Da mesma forma, pode invocar **runAsync** antes que os ouvintes de comunicação sejam abertos ou mesmo construídos. Se for necessária alguma sincronização, é deixado como exercício para o implementador. Aqui estão algumas soluções comuns:

  - Às vezes, os ouvintes não podem funcionar até que outra informação seja criada ou o trabalho seja feito. Para serviços apátridas, esse trabalho pode geralmente ser feito em outros locais, tais como: 
    - No construtor do serviço.
    - Durante a `CreateServiceInstanceListeners()` chamada.
    - Como parte da construção do próprio ouvinte.
  - Às vezes, o código em **RunAsync** só começa quando os ouvintes estiverem abertos. Neste caso, é necessária uma coordenação adicional. Uma solução comum é que há uma bandeira dentro dos ouvintes que indica quando terminaram. Esta bandeira é então verificada em **RunAsync** antes de continuar a trabalhar.

## <a name="stateless-service-shutdown"></a>Paralisação do serviço apátrida
Para desligar um serviço apátrida, segue-se o mesmo padrão, ao contrário:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado a cada ouvinte.
    - O sinal de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade de `IsCancellationRequested` do token de cancelamento retorna verdadeira, e se for chamado, o método de `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Após `CloseAsync()` termina em cada ouvinte e `RunAsync()` também termina, o método `StatelessService.OnCloseAsync()` do serviço é chamado, se presente.  OnCloseAsync é chamado quando a instância de serviço apátrida vai ser graciosamente encerrada. Isto pode ocorrer quando o código do serviço está a ser atualizado, a instância de serviço está a ser movida devido ao equilíbrio de carga, ou é detetada uma falha transitória. É incomum sobrepor-se `StatelessService.OnCloseAsync()`, mas pode ser usado para fechar recursos com segurança, parar o processamento de antecedentes, terminar a poupança de estado externo ou encerrar as ligações existentes.
3. Após `StatelessService.OnCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-startup"></a>Startup de serviço sinuoso
Os serviços estatais têm um padrão semelhante aos serviços apátridas, com algumas mudanças. Para iniciar um serviço imponente, a ordem dos eventos é a seguinte:

1. O serviço é construído.
2. `StatefulServiceBase.OnOpenAsync()` chama-se. Esta chamada não é geralmente ultrapassada no serviço.
3. As seguintes coisas acontecem em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado. 
      - Se o serviço for um serviço primário, todos os ouvintes devolvidos estão abertos. `ICommunicationListener.OpenAsync()` é chamado a cada ouvinte.
      - Se o serviço for um serviço secundário, apenas os ouvintes marcados como `ListenOnSecondary = true` são abertos. Ter ouvintes abertos em secundários é menos comum.
    - Se o serviço for atualmente um Método Primário, o método de `StatefulServiceBase.RunAsync()` do serviço é chamado.
4. Depois de todas as chamadas de `OpenAsync()` do ouvinte de réplica e `RunAsync()` é chamada, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

À semelhança dos serviços apátridas, não há coordenação entre a ordem em que os ouvintes são criados e abertos e quando **o RunAsync** é chamado. Se precisar de coordenação, as soluções são as mesmas. Há um caso adicional para o serviço de estado. Diga que as chamadas que chegam aos ouvintes de comunicação requerem informações mantidas dentro de [algumas Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md)

   > [!NOTE]  
   > Como os ouvintes de comunicação podem abrir antes que as coleções fiáveis sejam legíveis ou relegáveis, e antes que **o RunAsync** possa começar, é necessária alguma coordenação adicional. A solução mais simples e comum é que os ouvintes de comunicação devolvam um código de erro que o cliente usa para saber para voltar a julgar o pedido.

## <a name="stateful-service-shutdown"></a>Paralisação de serviços audais
Tal como os serviços apátridas, os eventos de ciclo de vida durante o encerramento são os mesmos que durante o arranque, mas invertidos. Quando um serviço imponente está a ser encerrado, ocorrem os seguintes eventos:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado a cada ouvinte.
    - O sinal de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade de `IsCancellationRequested` do token de cancelamento retorna verdadeira, e se for chamado, o método de `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Depois de `CloseAsync()` termina em cada ouvinte e `RunAsync()` também termina, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

   > [!NOTE]  
   > A necessidade de esperar que **o RunAsync** termine só é necessária se esta réplica for uma réplica primária.

3. Após o método `StatefulServiceBase.OnChangeRoleAsync()` terminar, o método `StatefulServiceBase.OnCloseAsync()` é chamado. Esta chamada é uma sobreposição incomum, mas está disponível.
3. Após `StatefulServiceBase.OnCloseAsync()` acabamentos, o objeto de serviço é destruído.

## <a name="stateful-service-primary-swaps"></a>Trocas primárias de serviço sinuoso
Enquanto um serviço imponente está em execução, apenas as réplicas primárias desses serviços estatais têm os seus ouvintes de comunicação abertos e o seu método **RunAsync** chamado. Réplicas secundárias são construídas, mas não vêem mais chamadas. Enquanto um serviço imponente está em execução, a réplica que é atualmente a Primary pode mudar como resultado de falha ou otimização de equilíbrio de clusters. O que significa isto em termos dos eventos de ciclo de vida que uma réplica pode ver? O comportamento que a réplica audana mente depende se é a réplica a ser despromovida ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para as Primárias que é despromovida
Para a réplica primária que é despromovida, o Service Fabric precisa desta réplica para parar de processar mensagens e desistir de qualquer trabalho de fundo que esteja a fazer. Como resultado, este passo parece que aconteceu quando o serviço é desligado. Uma diferença é que o serviço não está destruído ou fechado porque permanece como secundário. As seguintes APIs são chamadas:

1. Paralelamente:
    - Todos os ouvintes abertos estão fechados. `ICommunicationListener.CloseAsync()` é chamado a cada ouvinte.
    - O sinal de cancelamento passado para `RunAsync()` é cancelado. Uma verificação da propriedade de `IsCancellationRequested` do token de cancelamento retorna verdadeira, e se for chamado, o método de `ThrowIfCancellationRequested` do token lança um `OperationCanceledException`.
2. Depois de `CloseAsync()` termina em cada ouvinte e `RunAsync()` também termina, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para o Secundário que é promovido
Da mesma forma, o Service Fabric precisa da réplica secundária que é promovida para começar a ouvir mensagens no fio e iniciar quaisquer tarefas de fundo que precise de completar. Como resultado, este processo parece ter sido feito quando o serviço é criado, exceto que a réplica em si já existe. As seguintes APIs são chamadas:

1. Paralelamente:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` é invocado e todos os ouvintes devolvidos são abertos. `ICommunicationListener.OpenAsync()` é chamado a cada ouvinte.
    - O método de `StatefulServiceBase.RunAsync()` do serviço é chamado.
2. Depois de todas as chamadas de `OpenAsync()` do ouvinte de réplica e `RunAsync()` é chamada, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é geralmente ultrapassada no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Questões comuns durante a paragem do serviço e despromoção primária
O Tecido de Serviço altera a Primária de um serviço imponente por uma variedade de razões. Os mais comuns são o [reequilíbrio](service-fabric-cluster-resource-manager-balancing.md) do cluster e [a atualização da aplicação.](service-fabric-application-upgrade.md) Durante estas operações (assim como durante o encerramento normal do serviço, como veria se o serviço foi apagado), é importante que o serviço respeite o `CancellationToken`. 

Os serviços que não lidam com o cancelamento de forma limpa podem experimentar vários problemas. Estas operações são lentas porque o Service Fabric espera que os serviços parem graciosamente. Isto pode, em última análise, levar a atualizações falhadas que o tempo fora e reverter. A não honração do símbolo de cancelamento também pode causar aglomerados desequilibrados. Os aglomerados ficam desequilibrados porque os nós ficam quentes, mas os serviços não podem ser reequilibrados porque demora muito tempo a movê-los para outro lugar. 

Como os serviços são imponentes, também é provável que utilizem as [Coleções Fiáveis.](service-fabric-reliable-services-reliable-collections.md) No Tecido de Serviço, quando uma Primária é despromovida, uma das primeiras coisas que acontece é que o acesso escrito ao estado subjacente é revogado. Isto leva a um segundo conjunto de questões que podem afetar o ciclo de vida do serviço. As coleções devolvem exceções com base no tempo e se a réplica está a ser movida ou encerrada. Estas exceções devem ser corretamente tratadas. As exceções lançadas pelo Tecido de Serviço enquadram-se em categorias permanentes [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitórias ( [`FabricTransientException`).](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) As exceções permanentes devem ser registadas e lançadas, enquanto as exceções transitórias podem ser novamente tentadas com base numa lógica de retry.

Lidar com as exceções que derem da utilização do `ReliableCollections` em conjunto com eventos de ciclo de vida de serviço é uma parte importante do teste e validação de um Serviço Fiável. Recomendamos que execute sempre o seu serviço sob carga enquanto realiza upgrades e testes de [caos](service-fabric-controlled-chaos.md) antes de ser implementado para produção. Estes passos básicos ajudam a garantir que o seu serviço está corretamente implementado e lida corretamente com os eventos do ciclo de vida.


## <a name="notes-on-the-service-lifecycle"></a>Notas sobre o ciclo de vida de serviço
  - Tanto o método `RunAsync()` como as chamadas `CreateServiceReplicaListeners/CreateServiceInstanceListeners` são opcionais. Um serviço pode ter um deles, ambos, ou nenhum dos dois. Por exemplo, se o serviço fizer todo o seu trabalho em resposta às chamadas dos utilizadores, não há necessidade de implementá-lo `RunAsync()`. Apenas os ouvintes de comunicação e o seu código associado são necessários. Da mesma forma, criar e devolver ouvintes de comunicação é opcional, uma vez que o serviço só pode ter trabalho de fundo para fazer, pelo que só precisa de implementar `RunAsync()`.
  - É válido para um serviço para completar `RunAsync()` com sucesso e voltar dele. Completar não é uma condição de falha. Completar `RunAsync()` indica que o trabalho de fundo do serviço terminou. Para serviços estatais de confiança, `RunAsync()` é novamente chamado se a réplica for despromovida da Primária para a Secundária e depois promovida de volta para a Primária.
  - Se um serviço sair de `RunAsync()` lançando alguma exceção inesperada, isto constitui uma falha. O objeto de serviço é desligado e um erro de saúde é reportado.
  - Embora não exista um limite de tempo para o regresso destes métodos, você imediatamente perde a capacidade de escrever para Coleções Confiáveis, e, portanto, não pode completar qualquer trabalho real. Recomendamos que regresse o mais rápido possível ao receber o pedido de cancelamento. Se o seu serviço não responder a estas chamadas API num período razoável de tempo, o Service Fabric pode rescindir à força o seu serviço. Normalmente isto só acontece durante as atualizações da aplicação ou quando um serviço está a ser apagado. Este intervalo é de 15 minutos por defeito.
  - Falhas no caminho `OnCloseAsync()` resultam na `OnAbort()` ser chamada, o que é uma oportunidade de melhor esforço para o serviço limpar e libertar todos os recursos que eles reivindicaram. Isto é geralmente chamado quando uma falha permanente é detetada no nó, ou quando o Tecido de Serviço não pode gerir de forma fiável o ciclo de vida da instância de serviço devido a falhas internas.
  - `OnChangeRoleAsync()` é chamado quando a réplica do serviço está a mudar de papel, por exemplo, para primária ou secundária. As réplicas primárias recebem o estatuto de escrita (são autorizadas a criar e escrever para Coleções Fiáveis). As réplicas secundárias recebem o estatuto de leitura (só podem ser lidas a partir das coleções fiáveis existentes). A maioria dos trabalhos num serviço de estado é realizado na réplica primária. As réplicas secundárias podem realizar validação apenas de leitura, geração de relatórios, mineração de dados ou outros trabalhos apenas de leitura.

## <a name="next-steps"></a>Passos seguintes
- [Introdução a Serviços Fiáveis](service-fabric-reliable-services-introduction.md)
- [Início rápido dos Serviços Fiáveis](service-fabric-reliable-services-quick-start.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
