---
title: Diagnóstico e monitorização de atores
description: Este artigo descreve os diagnósticos e funcionalidades de monitorização do desempenho no tempo de funcionamento dos Atores Fiáveis do Tecido de Serviço, incluindo os eventos e contadores de desempenho emitidos por ele.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282331"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitorização do desempenho dos Reliable Actors
O tempo de execução dos Atores Fiáveis emite eventos [eventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre como o tempo de funcionamento está a funcionar e ajudam na resolução de problemas e monitorização do desempenho.

## <a name="eventsource-events"></a>EventoSEventosSource
O nome do fornecedor EventSource para o tempo de execução de Atores Fiáveis é "Microsoft-ServiceFabric-Actors". Eventos desta fonte do evento aparecem na janela [Eventos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) de Diagnóstico quando a aplicação do ator está a ser [depurada no Estúdio Visual.](service-fabric-debugging-your-application.md)

Exemplos de ferramentas e tecnologias que ajudam na recolha e/ou visualização de eventos De eventos São [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics,](../cloud-services/cloud-services-dotnet-diagnostics.md) [Semânticlogging](https://msdn.microsoft.com/library/dn774980.aspx)e a [Biblioteca Microsoft TraceEvent.](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem ao Reliable Actors EventSource estão associados a uma ou mais palavras-chave. Isto permite a filtragem dos eventos que são recolhidos. As seguintes palavras-chave são definidas.

| Bit | Descrição |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem o funcionamento dos Atores de Tecido. |
| 0x2 |Conjunto de eventos que descrevem chamadas de método de ator. Para mais informações, consulte o [tema introdutório sobre os atores.](service-fabric-reliable-actors-introduction.md) |
| 0x4 |Conjunto de eventos relacionados com o estado do ator. Para mais informações, consulte o tema da [gestão do Estado](service-fabric-reliable-actors-state-management.md)do ator. |
| 0x8 |Conjunto de eventos relacionados com a conmoeda baseada na reviravolta no ator. Para mais informações, consulte o tema da [moeda.](service-fabric-reliable-actors-introduction.md#concurrency) |

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução dos Atores Fiáveis define as seguintes categorias de contra-desempenho.

| Categoria | Descrição |
| --- | --- |
| Ator de tecido de serviço |Contadores específicos dos atores da Azure Service Fabric, por exemplo, tempo demorou a salvar o estado do ator |
| Método do ator de tecido de serviço |Contra-ataques específicos aos métodos implementados pelos atores do Service Fabric, por exemplo, quantas vezes um método de ator é invocado |

Cada uma das categorias acima tem um ou mais balcões.

A aplicação [Do Windows Performance Monitor,](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operativo Windows, pode ser utilizada para recolher e visualizar dados de contra-desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tem um grande número de serviços de ator ou partições de serviços de ator terá um grande número de casos de desempenho de ator. Os nomes de contra-instância de desempenho podem ajudar na identificação do método específico [de partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e ator (se aplicável) com o qual a contra-instância de desempenho está associada.

#### <a name="service-fabric-actor-category"></a>Categoria ator de tecido de serviço
Para a `Service Fabric Actor`categoria, os nomes de contra-instância estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) e a sua representação de cordas é gerada através do método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cordas de um inteiro de 64 bits que é gerado pelo tempo de execução dos Atores de Tecido para o seu uso interno. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` está a representação de cordas `635650083799324046` do ID de partição de tecido de serviço, e é o ID de 64 bits que é gerado para o uso interno do tempo de execução.

#### <a name="service-fabric-actor-method-category"></a>Categoria de Método de Ator de Tecido de Serviço
Para a `Service Fabric Actor Method`categoria, os nomes de contra-instância estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método do ator com o que a contra-instância de desempenho está associada. O formato do nome do método é determinado com base em alguma lógica no tempo de execução dos Atores de Tecido que equilibra a legibilidade do nome com restrições no comprimento máximo dos nomes de contadores de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de uma prótese de 32 bits que é gerada pelo tempo de execução dos Atores de Tecido para o seu uso interno. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) e a sua representação de cordas é gerada através do método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cordas de um inteiro de 64 bits que é gerado pelo tempo de execução dos Atores de Tecido para o seu uso interno. Isto está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outros nomes de contra-instância de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome da contra-instância de desempenho.

Segue-se um exemplo de um nome de contra-instância para um contador que pertence à `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo `ivoicemailboxactor.leavemessageasync` acima, é o `2` nome do método, é o ID de 32 `89383d32-e57e-4a9b-a6ad-57c6792aa521` bits gerado para o uso `635650083804480486` interno do tempo de execução, é a representação de cadeia do ID de divisória de tecido de serviço, e é o ID de 64 bits gerado para o uso interno do tempo de execução.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de ator e contadores de desempenho
O tempo de execução dos Atores Fiáveis emite os seguintes eventos relacionados com métodos de [ator.](service-fabric-reliable-actors-introduction.md)

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verboso |0x2 |O tempo de execução dos atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Verboso |0x2 |Um método de ator terminou a execução. Ou seja, a chamada assíncrona do tempo de execução ao método do ator voltou, e a tarefa devolvida pelo método do ator terminou. |
| ActorMethodThrewException |9 |Aviso |0x3 |Uma exceção foi lançada durante a execução de um método de ator, quer durante a chamada assíncrona do tempo de execução ao método do ator, quer durante a execução da tarefa devolvida pelo método do ator. Este evento indica algum tipo de falha no código do ator que precisa de investigação. |

O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados com a execução de métodos de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método do ator de tecido de serviço |Invocações/Sec |Número de vezes que o método de serviço do ator é invocado por segundo |
| Método do ator de tecido de serviço |Milissegundos médios por invocação |Tempo deexecução o método de serviço do ator em milissegundos |
| Método do ator de tecido de serviço |Exceções lançadas/Sec |Número de vezes que o método de serviço do ator abriu uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Eventos condivisas e contadores de desempenho
O tempo de execução dos Atores Fiáveis emite os seguintes eventos relacionados com a [conmoeda.](service-fabric-reliable-actors-introduction.md#concurrency)

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActormethodCallsWaitingForLock |12 |Verboso |0x8 |Este evento é escrito no início de cada nova volta em um ator. Contém o número de chamadas de ator esgotadas que estão à espera para adquirir o bloqueio por ator que impõe a moeda baseada em turnos. |

O tempo de execução dos Atores Fiáveis publica os seguintes contadores de desempenho relacionados com a conmoeda.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de tecido de serviço |# de chamadas de ator à espera de bloqueio de ator |Número de chamadas de ator pendente à espera de adquirir o bloqueio por ator que impõe a conmoeda baseada em turnos |
| Ator de tecido de serviço |Média de milissegundos por espera de bloqueio |Tempo demorou (em milissegundos) a adquirir o bloqueio por ator que impõe a moeda baseada em turnos |
| Ator de tecido de serviço |Bloqueio de ator de milissegundos médio mantido |Tempo (em milissegundos) para o qual se mantém a fechadura por ator |

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gestão do estado do ator e contadores de desempenho
The Reliable Actors runtime emite os seguintes eventos relacionados com a [gestão do estado](service-fabric-reliable-actors-state-management.md)do ator.

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verboso |0x4 |Os atores estão prestes a salvar o estado do ator. |
| ActorSaveStateStop |11 |Verboso |0x4 |Os atores terminaram de salvar o estado do ator. |

The Reliable Actors runtime publica os seguintes contadores de desempenho relacionados com a gestão do estado do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de tecido de serviço |Milissegundos médios por operação estatal de salvamento |Tempo tomado para salvar estado de ator em milissegundos |
| Ator de tecido de serviço |Média de milissegundos por operação estatal de carga |Tempo tomado para carregar estado ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados com réplicas de ator
The Reliable Actors runtime emite os seguintes eventos relacionados com réplicas de [atores](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| RéplicaChangeRoleToPrimary |1 |Informativo |0x1 |A réplica do ator mudou de papel para Primária. Isto implica que os atores desta partição serão criados dentro desta réplica. |
| RéplicaChangeRoleFromPrimary |2 |Informativo |0x1 |A réplica do ator mudou de papel para não-Primária. Isto implica que os atores desta partição deixarão de ser criados dentro desta réplica. Não serão entregues novos pedidos aos atores já criados dentro desta réplica. Os atores serão destruídos depois de todos os pedidos em curso estarem concluídos. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Ativação de atores e desativação de eventos e contadores de desempenho
O tempo de execução dos Atores Fiáveis emite os seguintes eventos relacionados com a [ativação e desativação](service-fabric-reliable-actors-lifecycle.md)do ator.

| Nome do evento | ID do Evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorAtivado |5 |Informativo |0x1 |Um ator foi ativado. |
| ActorDeactivado |6 |Informativo |0x1 |Um ator foi desativado. |

O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados com a ativação e desativação do ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de tecido de serviço |Média OnActivateAsync milissegundos |Tempo deexecução método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Ator solicita processamento de contadores de desempenho
Quando um cliente invoca um método através de um objeto de procuração de ator, resulta numa mensagem de pedido enviada através da rede para o serviço de ator. O serviço processa a mensagem de pedido e envia uma resposta ao cliente. O tempo de execução de Reliable Actors publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator de tecido de serviço |# de pedidos pendentes |Número de pedidos a serem processados no serviço |
| Ator de tecido de serviço |Milissegundos médios por pedido |Tempo tomado (em milissegundos) pelo serviço para processar um pedido |
| Ator de tecido de serviço |Milissegundos médios para desserialização de pedidos |Tempo demorou (em milissegundos) a desserializar mensagem de pedido de ator quando é recebida no serviço |
| Ator de tecido de serviço |Milissegundos médios para a serialização da resposta |Tempo demorou (em milissegundos) a serializar a mensagem de resposta do ator no serviço antes da resposta ser enviada ao cliente |

## <a name="next-steps"></a>Passos seguintes
* [Como os atores confiáveis usam a plataforma De Tecido de Serviço](service-fabric-reliable-actors-platform.md)
* [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código da amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Fornecedores de EventSource em PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
