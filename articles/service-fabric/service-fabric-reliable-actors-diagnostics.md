---
title: Diagnóstico e monitorização de atores
description: Este artigo descreve os recursos de monitoramento de desempenho e diagnóstico no Service Fabric Reliable Actors Runtime, incluindo os eventos e contadores de desempenho emitidos por ele.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75376737"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitorização do desempenho dos Reliable Actors
O tempo de execução de Reliable Actors emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Elas fornecem informações sobre como o tempo de execução está operando e ajudam na solução de problemas e no monitoramento de desempenho.

## <a name="eventsource-events"></a>Eventos EventSource
O nome do provedor EventSource para o tempo de execução de Reliable Actors é "Microsoft-Service Fabric-atores". Os eventos dessa origem do evento aparecem na janela [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o aplicativo ator está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou exibir eventos EventSource são [Perfview](https://www.microsoft.com/download/details.aspx?id=28567), [diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [log semântico](https://msdn.microsoft.com/library/dn774980.aspx)e a [biblioteca do Microsoft TraceEvent](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem ao Reliable Actors EventSource estão associados a uma ou mais palavras-chave. Isso permite a filtragem de eventos que são coletados. Os bits de palavra-chave a seguir são definidos.

| Parte | Descrição |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem a operação do tempo de execução dos atores do fabric. |
| 0x2 |Conjunto de eventos que descrevem chamadas de método de ator. Para obter mais informações, consulte o [tópico introdutório sobre atores](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Conjunto de eventos relacionados ao estado do ator. Para obter mais informações, consulte o tópico sobre [Gerenciamento de estado de ator](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Conjunto de eventos relacionados à simultaneidade baseada em troca no ator. Para obter mais informações, consulte o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução de Reliable Actors define as seguintes categorias de contador de desempenho.

| Categoria | Descrição |
| --- | --- |
| Ator de Service Fabric |Contadores específicos do Azure Service Fabric atores, por exemplo, tempo necessário para salvar o estado do ator |
| Método de ator de Service Fabric |Contadores específicos para métodos implementados por Service Fabric atores, por exemplo, com que frequência um método de ator é invocado |

Cada uma das categorias acima tem um ou mais contadores.

O aplicativo de [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operacional Windows pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los em tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instância do contador de desempenho
Um cluster que tem um grande número de serviços de ator ou partições de serviço de ator terá um grande número de instâncias de contador de desempenho de ator. Os nomes de instância do contador de desempenho podem ajudar a identificar a [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) específica e o método de ator (se aplicável) a que a instância do contador de desempenho está associada.

#### <a name="service-fabric-actor-category"></a>Categoria de ator Service Fabric
Para a categoria `Service Fabric Actor`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução dos atores de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir um exemplo de um nome de instância de contador para um contador que pertence à categoria de `Service Fabric Actor`:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de caracteres da ID de partição de Service Fabric e `635650083799324046` é a ID de 64 bits gerada para uso interno do tempo de execução.

#### <a name="service-fabric-actor-method-category"></a>Categoria do método de ator Service Fabric
Para a categoria `Service Fabric Actor Method`, os nomes de instância do contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método de ator ao qual a instância do contador de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução de atores de malha que equilibra a legibilidade do nome com restrições no tamanho máximo dos nomes de instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de cadeia de caracteres de um inteiro de 32 bits que é gerado pelo tempo de execução dos atores de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio do método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de caracteres de um inteiro de 64 bits que é gerado pelo tempo de execução dos atores de malha para seu uso interno. Isso é incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os usuários não devem tentar interpretar essa parte do nome da instância do contador de desempenho.

Veja a seguir um exemplo de um nome de instância de contador para um contador que pertence à categoria de `Service Fabric Actor Method`:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, `2` é a ID de 32 bits gerada para uso interno do tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de caracteres da ID de partição Service Fabric e `635650083804480486` é a ID de 64 bits gerada para uso interno do tempo de execução.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos do método de ator e contadores de desempenho
O tempo de execução de Reliable Actors emite os eventos a seguir relacionados aos [métodos de ator](service-fabric-reliable-actors-introduction.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verboso |0x2 |O tempo de execução dos atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Verboso |0x2 |Um método de ator concluiu a execução. Ou seja, a chamada assíncrona do tempo de execução para o método de ator foi retornada e a tarefa retornada pelo método de ator foi concluída. |
| ActorMethodThrewException |9 |Aviso |0x3 |Uma exceção foi lançada durante a execução de um método de ator, seja durante a chamada assíncrona do tempo de execução para o método de ator ou durante a execução da tarefa retornada pelo método de ator. Esse evento indica algum tipo de falha no código de ator que precisa de investigação. |

O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados à execução de métodos de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de ator de Service Fabric |Invocações/s |Número de vezes que o método de serviço de ator é invocado por segundo |
| Método de ator de Service Fabric |Média de milissegundos por invocação |Tempo necessário para executar o método de serviço de ator em milissegundos |
| Método de ator de Service Fabric |Exceções geradas/s |Número de vezes que o método de serviço de ator lançou uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Eventos de simultaneidade e contadores de desempenho
O tempo de execução de Reliable Actors emite os eventos a seguir relacionados à [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verboso |0x8 |Esse evento é escrito no início de cada novo ativar um ator. Ele contém o número de chamadas de ator pendentes que estão aguardando para adquirir o bloqueio por ator que impõe a simultaneidade baseada em um desligamento. |

O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados à simultaneidade.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de Service Fabric |n º de chamadas de ator aguardando bloqueio de ator |Número de chamadas de ator pendentes aguardando para adquirir o bloqueio por ator que impõe a simultaneidade baseada em desligamento |
| Ator de Service Fabric |Média de milissegundos por espera de bloqueio |Tempo decorrido (em milissegundos) para adquirir o bloqueio por ator que impõe a simultaneidade baseada em desligamento |
| Ator de Service Fabric |Média de bloqueio de ator em milissegundos mantido |Tempo (em milissegundos) para o qual o bloqueio por ator é mantido |

### <a name="actor-state-management-events-and-performance-counters"></a>Contadores de desempenho e eventos de gerenciamento de estado do ator
O tempo de execução de Reliable Actors emite os eventos a seguir relacionados ao [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verboso |0x4 |O tempo de execução dos atores está prestes a salvar o estado do ator. |
| ActorSaveStateStop |11 |Verboso |0x4 |O tempo de execução dos atores terminou de salvar o estado do ator. |

O tempo de execução do Reliable Actors publica os seguintes contadores de desempenho relacionados ao gerenciamento de estado do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de Service Fabric |Média de milissegundos por operação de estado de salvamento |Tempo necessário para salvar o estado do ator em milissegundos |
| Ator de Service Fabric |Média de milissegundos por operação de estado de carregamento |Tempo necessário para carregar o estado do ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados a réplicas de ator
O tempo de execução de Reliable Actors emite os eventos a seguir relacionados a [réplicas de ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informativo |0x1 |A réplica de ator alterou a função para primária. Isso implica que os atores dessa partição serão criados dentro desta réplica. |
| ReplicaChangeRoleFromPrimary |2 |Informativo |0x1 |A réplica de ator alterou a função para não primária. Isso implica que os atores dessa partição não serão mais criados dentro desta réplica. Nenhuma nova solicitação será entregue aos atores já criados nesta réplica. Os atores serão destruídos depois que todas as solicitações em andamento forem concluídas. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação de ator e contadores de desempenho
O tempo de execução de Reliable Actors emite os eventos a seguir relacionados à [ativação e à desativação de ator](service-fabric-reliable-actors-lifecycle.md).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informativo |0x1 |Um ator foi ativado. |
| ActorDeactivated |6 |Informativo |0x1 |Um ator foi desativado. |

O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados à ativação e à desativação de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de Service Fabric |Média de milissegundos de OnActivateAsync |Tempo necessário para executar o método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Contadores de desempenho de processamento de solicitação de ator
Quando um cliente chama um método por meio de um objeto proxy de ator, ele resulta em uma mensagem de solicitação sendo enviada pela rede para o serviço de ator. O serviço processa a mensagem de solicitação e envia uma resposta de volta ao cliente. O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados ao processamento de solicitação de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de Service Fabric |n º de solicitações pendentes |Número de solicitações sendo processadas no serviço |
| Ator de Service Fabric |Média de milissegundos por solicitação |Tempo decorrido (em milissegundos) pelo serviço para processar uma solicitação |
| Ator de Service Fabric |Média de milissegundos para desserialização de solicitação |Tempo decorrido (em milissegundos) para desserializar a mensagem de solicitação de ator quando ela é recebida no serviço |
| Ator de Service Fabric |Média de milissegundos para serialização de resposta |Tempo decorrido (em milissegundos) para serializar a mensagem de resposta de ator no serviço antes que a resposta seja enviada ao cliente |

## <a name="next-steps"></a>Passos seguintes
* [Como Reliable Actors usar a plataforma Service Fabric](service-fabric-reliable-actors-platform.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Provedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
