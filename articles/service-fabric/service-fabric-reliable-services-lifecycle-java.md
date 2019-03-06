---
title: Ciclo de vida do Service Fabric Reliable Services do Azure | Documentos da Microsoft
description: Saiba mais sobre os eventos de ciclo de vida no Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 4d97803117a875514fb36f770d551204ece0e55c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448956"
---
# <a name="reliable-services-lifecycle"></a>Ciclo de vida dos Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services é um dos modelos de programação disponíveis no Azure Service Fabric. Durante o aprendizado sobre o ciclo de vida dos Reliable Services, é mais importante compreender os eventos de ciclo de vida básica. A ordem exata de eventos depende de detalhes de configuração. 

Em geral, o ciclo de vida do Reliable Services inclui os seguintes eventos:

* Durante a inicialização:
  * Os serviços são construídos.
  * Os serviços têm uma oportunidade para construir e retornar zero ou mais serviços de escuta.
  * Quaisquer serviços de escuta retornados estão abertos para comunicação com o serviço.
  * O serviço `runAsync` método é chamado, para que o serviço pode fazer de longa execução ou em segundo plano de trabalho.
* Durante o encerramento:
  * O token de cancelamento que foi passado para `runAsync` é cancelada, e os serviços de escuta estão fechados.
  * O objeto de serviço em si é destructed.

A ordem dos eventos no Reliable Services podem ser alteradas ligeiramente dependendo do serviço fiável é com ou sem estado. 

Além disso, para serviços com estado, deve resolver o cenário de troca primário. Durante essa seqüência, a função de principal é transferida para outra réplica (ou volta) sem o serviço a ser encerrado. 

Por fim, precisa se preocupar sobre condições de erro ou falha.

## <a name="stateless-service-startup"></a>Arranque do serviço sem estado
O ciclo de vida de um serviço sem estado é bastante simples. Esta é a ordem dos eventos:

1. O serviço é construído.
2. Estes eventos ocorrem em paralelo:
    - `StatelessService.createServiceInstanceListeners()` é invocado, e qualquer devolvido de serviços de escuta estão abertos. `CommunicationListener.openAsync()` é chamado em cada serviço de escuta.
    - O serviço `runAsync` método (`StatelessService.runAsync()`) é chamado.
3. Se estiver presente, do serviço próprio `onOpenAsync` método é chamado. Especificamente, `StatelessService.onOpenAsync()` é chamado. Esta é uma substituição incomum, mas está disponível.

É importante ter em atenção que não há nenhum ordenação entre a chamada para criar e abrir os serviços de escuta e a chamada para `runAsync`. Os serviços de escuta podem abrir antes `runAsync` é iniciado. Da mesma forma, `runAsync` poderá ser invocado antes dos serviços de escuta de comunicação estão abertos ou antes que até mesmo ter sido construídos. Se qualquer sincronização for necessária, deve ser feito pelo implementador. Aqui estão algumas soluções comuns:

* Por vezes, serviços de escuta não podem funcionar enquanto outras informações são criadas ou outro trabalho é feito. Para serviços sem estado, que trabalho normalmente pode ser feito no construtor do serviço. Isso pode ser feito durante o `createServiceInstanceListeners()` chamar, ou como parte da construção do serviço de escuta em si.
* Por vezes, o código na `runAsync` não inicia até que os serviços de escuta estão abertos. Neste caso, coordenação adicional é necessária. Uma solução comum é adicionar um sinalizador os serviços de escuta. O sinalizador indica quando tem concluído os serviços de escuta. O `runAsync` método verifica isto antes de continuar o trabalho real.

## <a name="stateless-service-shutdown"></a>Encerramento do serviço sem estado
Ao encerrar um serviço sem estado, o mesmo padrão é seguido, mas inversa:

1. Estes eventos ocorrem em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento que foi passado para `runAsync()` é cancelada. A verificar o token de cancelamento `isCancelled` propriedade devolve `true`e se chamado, o token `throwIfCancellationRequested` método lança uma `CancellationException`.
2. Quando `closeAsync()` termina em cada serviço de escuta e `runAsync()` também estiver concluída, o serviço `StatelessService.onCloseAsync()` método é chamado, se estiver presente. Novamente, isso não é uma substituição comuns, mas pode ser usado com segurança, fechar recursos, parar processamento em segundo plano, concluir a guardar Estado externo ou fechar as ligações existentes.
3. Depois de `StatelessService.onCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-startup"></a>Arranque do serviço com estado
Serviços com estado tem um padrão que é semelhante aos serviços sem estado, com algumas alterações.  Esta é a ordem dos eventos para iniciar um serviço com estado:

1. O serviço é construído.
2. `StatefulServiceBase.onOpenAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.
3. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado. 
      - Se o serviço é um serviço principal, todos os serviços de escuta retornados são abertos. `CommunicationListener.openAsync()` é chamado em cada serviço de escuta.
      - Se o serviço é um serviço secundário, apenas serviços de escuta é marcado como `listenOnSecondary = true` estão abertas. É menos comum ter ouvintes de que estão abertas no bases de dados secundárias.
    - Se o serviço está atualmente um site primário, o serviço `StatefulServiceBase.runAsync()` método é chamado.
4. Depois de todas o réplicas do serviço de escuta `openAsync()` chama finish e `runAsync()` é chamado, `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

Semelhante aos serviços sem estado, no serviço com estado, não há nenhum coordenação entre a ordem na qual os serviços de escuta são criados e abertos e quando `runAsync` é chamado. Se precisar de coordenação, as soluções são muito parecido com isso. Mas há um caso adicional para serviço com estado. Digamos que as chamadas que deparar-se com os serviços de escuta de comunicação requerem que as informações mantidas dentro de alguns [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Uma vez que os serviços de escuta de comunicação poderá ser aberta antes da Reliable Collections são legíveis ou gravável e antes de `runAsync` é iniciado, alguns coordenação adicional é necessária. A solução mais simples e mais comum é para os serviços de escuta de comunicação retornar um código de erro. O cliente utiliza o código de erro de saber para repetir o pedido.

## <a name="stateful-service-shutdown"></a>Encerramento do serviço com estado
Como serviços sem estado, os eventos de ciclo de vida durante o desligamento são os mesmos durante a inicialização, mas invertido. Quando um serviço com estado está a ser encerrado, ocorrem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento que foi passado para `runAsync()` é cancelada. Uma chamada para o token de cancelamento `isCancelled()` retorno do método `true`e se chamado, o token `throwIfCancellationRequested()` método lança uma `OperationCanceledException`.
2. Após `closeAsync()` termina em cada serviço de escuta e `runAsync()` também estiver concluída, o serviço `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

   > [!NOTE]  
   > A aguardar `runAsync` para concluir é necessário apenas se esta réplica é uma réplica primária.

3. Depois do `StatefulServiceBase.onChangeRoleAsync()` conclusão do método, o `StatefulServiceBase.onCloseAsync()` método é chamado. Esta chamada é uma substituição incomum, mas está disponível.
3. Depois de `StatefulServiceBase.onCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-primary-swaps"></a>Troca de principal de serviço com estado
Durante a execução de um serviço com estado, os serviços de escuta de comunicação são abertos e o `runAsync` método é chamado apenas para as réplicas principal de que os serviços com estado. As réplicas secundárias são construídas, mas não mais chamadas de ver. Durante a execução de um serviço com estado, a réplica que atualmente é o principal pode alterar. Os eventos de ciclo de vida que pode ver uma réplica com monitoração de estado depende se é a réplica a ser despromovida ou promovida durante a troca.

### <a name="for-the-demoted-primary"></a>Para as primárias despromovidas
Service Fabric tem da réplica primária, que é rebaixada para parar o processamento das mensagens e parar qualquer trabalho em segundo plano. Este passo é semelhante a quando o serviço é encerrado. Uma diferença é que o serviço não estiver destructed ou fechado, porque ele continua sendo como um secundário. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `CommunicationListener.closeAsync()` é chamado em cada serviço de escuta.
    - O token de cancelamento que foi passado para `runAsync()` é cancelada. Uma verificação do token de cancelamento `isCancelled()` retorno do método `true`. Se chamado, o token `throwIfCancellationRequested()` método lança uma `OperationCanceledException`.
2. Após `closeAsync()` termina em cada serviço de escuta e `runAsync()` também estiver concluída, o serviço `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

### <a name="for-the-promoted-secondary"></a>Para o secundário promovido
Da mesma forma, o Service Fabric tem a réplica secundária que é promovida para começar a ouvir as mensagens na conexão e iniciar quaisquer tarefas em segundo plano que necessita para concluir. Este processo é semelhante a quando o serviço é criado. A diferença é que a réplica em si já existe. Ocorrerem os seguintes eventos:

1. Estes eventos ocorrem em paralelo:
    - `StatefulServiceBase.createServiceReplicaListeners()` é invocado e qualquer devolvido de serviços de escuta estão abertos. `CommunicationListener.openAsync()` é chamado em cada serviço de escuta.
    - O serviço `StatefulServiceBase.runAsync()` método é chamado.
2. Depois de todas o réplicas do serviço de escuta `openAsync()` chama finish e `runAsync()` é chamado, `StatefulServiceBase.onChangeRoleAsync()` é chamado. Esta chamada não é substituída, geralmente no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o encerramento do serviço com monitorização de estado e despromoção primária
Service Fabric altera o principal de serviço com estado por vários motivos. Os motivos mais comuns são [cluster reequilibrar](service-fabric-cluster-resource-manager-balancing.md) e [atualização da aplicação](service-fabric-application-upgrade.md). Durante essas operações, é importante que o serviço respeita o `cancellationToken`. Isto também se aplica durante o encerramento do serviço normal, por exemplo, se o serviço foi eliminado.

Serviços que não lidar corretamente com o cancelamento podem assistir a vários problemas. Essas operações são lentas, porque o Service Fabric aguarda para os serviços parar corretamente. Em última análise, isto pode levar a atualizações com falha esse limite de tempo e reversão. Falha que respeite o token de cancelamento também pode fazer com que os clusters desequilibrados. Clusters ficarem desequilibrados porque nós obtém acesso frequente. No entanto, os serviços não podem ser reequilibrados porque demora muito tempo para movê-los em outro lugar. 

Como os serviços são com monitoração de estado, também é provável que os serviços utilizam [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um site primário é despromovido, uma das primeiras coisas que acontece é que o acesso de escrita para o estado subjacente é revogado. Isso nos leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. A coleções de retornadas de exceções com base no período de tempo e se a réplica está a ser movida ou encerramento. É importante lidar com essas exceções corretamente. 

Exceções geradas pelo Service Fabric são permanentes [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) ou transitória [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Permanentes exceções devem ser registadas e lançadas. Exceções transitórias podem ser repetidas com base na lógica de repetição.

Uma parte importante de testar e validar a Reliable Services está a processar as exceções que vêm de utilizar o `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço. Recomendamos que execute sempre a seu serviço sob carga. Também deve executar as atualizações e [chaos teste](service-fabric-controlled-chaos.md) antes da implantação em produção. Estas etapas básicas ajudam a garantir que seu serviço está implementado corretamente e que processa os eventos de ciclo de vida corretamente.

## <a name="notes-on-service-lifecycle"></a>Notas sobre o ciclo de vida do serviço
* Ambas as `runAsync()` método e o `createServiceInstanceListeners/createServiceReplicaListeners` chamadas são opcionais. Um serviço pode ter um, ambos ou nenhum. Por exemplo, se o serviço faz todo o trabalho em resposta a chamadas de utilizador, não é necessário para o mesmo implementar `runAsync()`. Apenas os serviços de escuta de comunicação e o respetivo código associado são necessários.  Da mesma forma, criação e o retorno de serviços de escuta de comunicação são opcional. O serviço pode ter apenas em segundo plano de trabalho, portanto, ele só precisa de implementar `runAsync()`.
* Ele é válido para um serviço concluir `runAsync()` com êxito e retorno do mesmo. Isso não é considerado uma condição de falha. Ele representa o trabalho em segundo plano de finalizar o serviço. Para Reliable Services com estado, `runAsync()` seriam chamado novamente, se o serviço é despromovido da primária e, em seguida, é promovido para principal.
* Se sair de um serviço de `runAsync()` lançando uma exceção inesperada, esta é uma falha. O objeto de serviço é encerrado e é comunicado um erro de estado de funcionamento.
* Embora não exista nenhum limite de tempo no retorno entre esses métodos, perde imediatamente a capacidade de escrever. Por conseguinte, não é possível concluir todo o trabalho real. Recomendamos que retornar o mais rapidamente possível após receber a solicitação de cancelamento. Se o seu serviço não responder a essas chamadas de API num período de tempo razoável, o Service Fabric pode encerrar a forçar seu serviço. Normalmente, isto acontece apenas durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por predefinição.
* Falhas no `onCloseAsync()` resultado de caminho no `onAbort()` a ser chamado. Esta chamada é uma oportunidade de última oportunidade, de melhor esforço para o serviço de limpeza e liberar quaisquer recursos que eles solicitou. Isso geralmente é chamado quando for detetada uma falha de permanente no nó, ou quando o Service Fabric com confiança não é possível gerir o ciclo de vida da instância de serviço devido a falhas internas.
* `OnChangeRoleAsync()` é chamado quando a réplica de serviço com estado está mudando função, por exemplo para a primária ou secundária. Réplicas primárias recebem o estado de escrita (têm permissão para criar e escrever Reliable Collections). As réplicas secundárias recebem o estado de leitura (só pode ler a partir de coleções fiáveis existente). Maior parte do trabalho num serviço com monitorização de estado é efetuada na réplica primária. As réplicas secundárias podem efetuar a validação de só de leitura, geração de relatórios, mineração de dados ou outras tarefas só de leitura.

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Reliable Services](service-fabric-reliable-services-introduction.md)
* [Guia de introdução de serviços fiável](service-fabric-reliable-services-quick-start-java.md)

