---
title: Diagnósticos de serviços fiáveis de serviços de serviço sinuoso de tecido azure
description: Funcionalidade de diagnóstico para serviços fiáveis e estatais em tecido de serviço Azure
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282266"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico dos Reliable Services com Monitorização de Estado
O Serviço Azure Fabric Stateful Reliable Services StatefulServiceBase emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que podem ser usados para desinpurar o serviço, fornecer informações sobre como o tempo de funcionamento está funcionando, e ajudar na resolução de problemas.

## <a name="eventsource-events"></a>EventoSEventosSource
O nome EventSource para a classe Stateful ServiceBase de Serviços Fiáveis é "Microsoft-ServiceFabric-Services". Eventos desta fonte do evento aparecem na janela [Eventos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) de Diagnóstico quando o serviço está a ser [depurado no Estúdio Visual](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam na recolha e/ou visualização de eventos De eventos São [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)e a [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID do Evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitido quando a tarefa runasync do serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitido quando a tarefa runasync do serviço é cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitido quando a tarefa runasync do serviço está terminada |
| StatefulRunAsyncSlowCancellation |4 |Aviso |Emitida quando a tarefa runasync do serviço demora muito tempo a concluir o cancelamento |
| StatefulRunAsyncFailure |5 |Erro |Emitido quando a tarefa RunAsync do serviço lança uma exceção |

## <a name="interpret-events"></a>Interpretar eventos
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncEventos Eventos são úteis para o escritor de serviço supor o ciclo de vida de um serviço, bem como o tempo para quando um serviço começa, cancela ou termina. Esta informação pode ser útil para depurar questões de serviço ou compreender o ciclo de vida do serviço.

Os escritores de serviços devem prestar muita atenção aos eventos StatefulRunAsyncSlowCancelamento e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitido sempre que a tarefa runAsync do serviço lança uma exceção. Normalmente, uma exceção lançada indica um erro ou bug no serviço. Além disso, a exceção faz com que o serviço falhe, pelo que é movido para um nó diferente. Esta operação pode ser dispendiosa e pode atrasar os pedidos de entrada enquanto o serviço é transferido. Os escritores de serviçodevem determinar a causa da exceção e, se possível, atenuá-la.

StatefulRunAsyncSlowCancelamento é emitido sempre que um pedido de cancelamento para a tarefa RunAsync demora mais de quatro segundos. Quando um serviço demora muito tempo a concluir o cancelamento, afeta a capacidade do serviço de ser rapidamente reiniciado noutro nó. Este cenário pode afetar a disponibilidade global do serviço.

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução dos Serviços Fiáveis define as seguintes categorias de contra-desempenho:

| Categoria | Descrição |
| --- | --- |
| Replicador transacional de tecido de serviço |Contadores específicos do replicador transacional de tecido de serviço Azure |
| TStore de tecido de serviço |Contadores específicos da TStore de Tecido de Serviço Azure |

O Replicador transacional de tecido de serviço é utilizado pelo [Gestor de Estado fiável](service-fabric-reliable-services-reliable-collections-internals.md) para replicar transações num determinado conjunto de [réplicas](service-fabric-concepts-replica-lifecycle.md).

O Service Fabric TStore é um componente utilizado em [Coleções Fiáveis](service-fabric-reliable-services-reliable-collections-internals.md) para armazenar e recuperar pares de valor-chave.

A aplicação [Do Windows Performance Monitor,](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operativo Windows, pode ser utilizada para recolher e visualizar dados de contra-desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tenha um grande número de serviços fiáveis ou divisórias de serviço fiáveis terá um grande número de casos de contra-ocorrências de replicadores transacionais. Este é também o caso dos contadores de desempenho da TStore, mas também é multiplicado pelo número de Dicionários Fiáveis e Filas Fiáveis utilizadas. Os nomes de contadores de desempenho podem ajudar na identificação da [partição](service-fabric-concepts-partitioning.md)específica, réplica do serviço e fornecedor estatal no caso da TStore, a que a contra-instância de desempenho está associada.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoria replicador de transação de tecido de serviço
Para a categoria `Service Fabric Transactional Replicator`, os nomes de contra-instância estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, e a sua representação de cordas é gerada através [de`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado a uma determinada réplica de um serviço fiável. O ID da réplica está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outras contra-instâncias de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e o seu papel em serviços fiáveis podem ser encontrados [aqui.](service-fabric-concepts-replica-lifecycle.md)

O seguinte nome de contra-instância é típico de um contador na categoria `Service Fabric Transactional Replicator`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cordas do ID de partição de tecido de serviço, e `131652217797162571` é a réplica ID.

#### <a name="service-fabric-tstore-category"></a>Categoria TStore de Tecido de Serviço
Para a categoria `Service Fabric TStore`, os nomes de contra-instância estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* é a representação de cadeia do ID de partição de tecido de serviço a que a contra-instância de desempenho está associada. O ID de partição é um GUID, e a sua representação de cordas é gerada através [de`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado a uma determinada réplica de um serviço fiável. O ID da réplica está incluído no nome de contador de desempenho para garantir a sua singularidade e evitar conflitos com outras contra-instâncias de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e o seu papel em serviços fiáveis podem ser encontrados [aqui.](service-fabric-concepts-replica-lifecycle.md)

*StateProviderId* é o ID associado a um fornecedor estatal dentro de um serviço fiável. O ID do fornecedor estatal está incluído no nome de contador de desempenho para diferenciar uma TStore de outra.

*PerformanceCounterInstanceDifferentiator* é um ID diferenciador associado a uma contra instância de desempenho dentro de um provedor do Estado. Este diferenciador está incluído no nome da contra-instância de desempenho para garantir a sua singularidade e evitar conflitos com outras contra-instâncias de desempenho geradas pelo mesmo provedor do Estado.

*StateProviderName* é o nome associado a um fornecedor estatal dentro de um serviço fiável. O nome do fornecedor estatal está incluído no nome de exemplo de contra-desempenho para os utilizadores identificarem facilmente o estado que fornece.

O seguinte nome de contra-instância é típico de um contador na categoria `Service Fabric TStore`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia supor o ID de divisória de tecido de serviço, `131652217797162571` é a réplica ID, `142652217797162571` é o ID do fornecedor do Estado, e `1337` é o diferenciador de contrainstância de desempenho. `urn:MyReliableDictionary/dataStore` é o nome do provedor do Estado que armazena dados para a recolha denominada `urn:MyReliableDictionary`.

### <a name="transactional-replicator-performance-counters"></a>Contadores de desempenho do replicador transacional

O tempo de execução dos Serviços Fiáveis emite os seguintes eventos na categoria `Service Fabric Transactional Replicator`

 Nome do contador | Descrição |
| --- | --- |
| Iniciar operações Txn/seg | O número de novas transações escritas criadas por segundo.|
| Operações Txn/seg | O número de operações de adição/atualização/eliminação realizadas em coleções fiáveis por segundo.|
| Log Flush Bytes/seg | O número de bytes a ser lavado ao disco pelo Replicador Transacional por segundo |
| Operações aceleradas/seg | O número de operações rejeitadas a cada segundo pelo Replicador Transacional devido à aceleração. |
| Avg. Transação ms/Commit | Latência média de compromisso por transação em milissegundos |
| Avg. Latência flush (ms) | Duração média das operações de descarga de disco iniciadas pelo Replicador Transacional em milissegundos |

### <a name="tstore-performance-counters"></a>Contadores de desempenho da TStore

O tempo de execução dos Serviços Fiáveis emite os seguintes eventos na categoria `Service Fabric TStore`

 Nome do contador | Descrição |
| --- | --- |
| Número de itens | O número de artigos na loja.|
| Tamanho do Disco | O tamanho total do disco, em bytes, de ficheiros de controlo para a loja.|
| Ficheiro de verificação Write Bytes/seg | O número de bytes escritos por segundo para o mais recente ficheiro de controlo.|
| Copy Disk Transfer Bytes/seg | O número de bytes de disco lidos (na réplica primária) ou escritos (numa réplica secundária) por segundo durante uma cópia da loja.|

## <a name="next-steps"></a>Passos seguintes
[Fornecedores de EventSource em PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
