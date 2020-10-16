---
title: Diagnósticos de serviços fiáveis de tecido de serviços azures
description: Funcionalidade de diagnóstico para serviços fiáveis stateful em tecido de serviço Azure
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 5a3831dd4f8d5402980fac3daf8c35d9884c852d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840766"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico dos Reliable Services com Monitorização de Estado
A classe StatefulServiceBase, da Azure Service Stateful, emite eventos [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) que podem ser usados para depurar o serviço, fornecer informações sobre como o tempo de funcionamento está a funcionar e ajudar na resolução de problemas.

## <a name="eventsource-events"></a>Eventos EventSource
O nome EventSource para a classe Stateful Reliable Services StatefulServiceBase é "Microsoft-ServiceFabric-Services". Eventos desta fonte de eventos aparecem na janela [de Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está a ser [depurado no Estúdio Visual.](service-fabric-debugging-your-application.md)

Exemplos de ferramentas e tecnologias que ajudam na recolha e/ou visualização de eventos EventSource são [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)e Microsoft [TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID do Evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitida quando a tarefa runAsync de serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitida quando a tarefa runAsync de serviço é cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitida quando a tarefa runAsync de serviço estiver terminada |
| StatefulRunAsyncSlowCancellation |4 |Aviso |Emitida quando a tarefa runAsync de serviço demora muito tempo a concluir o cancelamento |
| StatefulRunAsyncFailure |5 |Erro |Emitida quando a tarefa runAsync de serviço lança uma exceção |

## <a name="interpret-events"></a>Interpretar eventos
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o escritor de serviço entender o ciclo de vida de um serviço, bem como o tempo para quando um serviço começa, cancela ou termina. Estas informações podem ser úteis para depurar problemas de serviço ou compreender o ciclo de vida do serviço.

Os autores de serviços devem prestar muita atenção aos eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitida sempre que a tarefa runAsync de serviço abre uma exceção. Normalmente, uma exceção lançada indica um erro ou erro no serviço. Além disso, a exceção faz com que o serviço falhe, pelo que é movido para um nó diferente. Esta operação pode ser dispendiosa e pode atrasar os pedidos de entrada enquanto o serviço é movido. Os autores de serviços devem determinar a causa da exceção e, se possível, atenuá-la.

StatefulRunAsyncSlowCancellation é emitido sempre que um pedido de cancelamento para a tarefa RunAsync demora mais de quatro segundos. Quando um serviço demora demasiado tempo a concluir o cancelamento, afeta a capacidade do serviço ser rapidamente reiniciado noutro nó. Este cenário pode afetar a disponibilidade global do serviço.

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução dos Serviços Fiáveis define as seguintes categorias de contadores de desempenho:

| Categoria | Descrição |
| --- | --- |
| Replicador transacional de tecido de serviço |Contadores específicos do Replicador Transacional de Tecido de Serviço Azure |
| TStore de tecido de serviço |Contadores específicos da Azure Service Fabric TStore |

O Replicador Transacional de Tecido de Serviço é utilizado pelo [Gestor estatal fiável](./service-fabric-reliable-services-introduction.md) para replicar transações dentro de um determinado conjunto de [réplicas.](service-fabric-concepts-replica-lifecycle.md)

O Service Fabric TStore é um componente utilizado em [Coleções Fiáveis](./service-fabric-reliable-services-introduction.md) para armazenar e recuperar pares de valores-chave.

A aplicação [Windows Performance Monitor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) que está disponível por padrão no sistema operativo Windows pode ser usada para recolher e visualizar dados de contador de desempenho. [O Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e enviá-lo para as tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de contra-instância de desempenho
Um cluster que tenha um grande número de serviços fiáveis ou divisórias de serviço fiáveis terá um grande número de casos de contador de desempenho de replicadores transacionais. Este é também o caso dos balcões de desempenho da TStore, mas também é multiplicado pelo número de Dicionários Fiáveis e Filas Fiáveis utilizadas. Os nomes do contador de desempenho podem ajudar a identificar a [partição](service-fabric-concepts-partitioning.md)específica, a réplica de serviço e o fornecedor estatal no caso da TStore, a que o contador de desempenho está associado.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoria de Replicador Transacional de Tecido de Serviço
Para a `Service Fabric Transactional Replicator` categoria, os nomes de contraexemplo estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) do especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado a uma réplica de um serviço fiável. O ID de réplica está incluído no nome do contador de desempenho para garantir a sua singularidade e evitar conflitos com outras instâncias de contador de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e o seu papel em serviços fiáveis podem ser encontrados [aqui.](service-fabric-concepts-replica-lifecycle.md)

O nome do seguinte contraexemplo é típico de um contador na `Service Fabric Transactional Replicator` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cordas do ID de partição do Tecido de Serviço, e `131652217797162571` é o ID de réplica.

#### <a name="service-fabric-tstore-category"></a>Categoria TStore de Tecido de Serviço
Para a `Service Fabric TStore` categoria, os nomes de contraexemplo estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* é a representação de cadeia do ID de partição do Tecido de Serviço a que o contador de desempenho está associado. O ID de partição é um GUID, e a sua representação de cordas é gerada através [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) do especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado a uma réplica de um serviço fiável. O ID de réplica está incluído no nome do contador de desempenho para garantir a sua singularidade e evitar conflitos com outras instâncias de contador de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e o seu papel em serviços fiáveis podem ser encontrados [aqui.](service-fabric-concepts-replica-lifecycle.md)

*StateProviderId* é o ID associado a um fornecedor estatal dentro de um serviço confiável. O ID do fornecedor estatal está incluído no nome do contador de desempenho para diferenciar uma TStore de outra.

*PerformanceCounterInstanceDifferentiator* é um ID diferenciador associado a uma contra-instância de desempenho dentro de um provedor do estado. Este diferenciador está incluído no nome do contador de desempenho para garantir a sua singularidade e evitar conflitos com outras contra-instâncias de desempenho geradas pelo mesmo provedor estatal.

*StateProviderName* é o nome associado a um prestador de serviços estatal dentro de um serviço fiável. O nome do fornecedor estatal está incluído no nome do contador de desempenho para os utilizadores identificarem facilmente o estado que fornece.

O nome do seguinte contraexemplo é típico de um contador na `Service Fabric TStore` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia do ID de partição de tecido de serviço, `131652217797162571` é o ID de réplica, `142652217797162571` é o ID do provedor do estado, e `1337` é o diferenciador de contraexemplo de desempenho. `urn:MyReliableDictionary/dataStore` é o nome do fornecedor estatal que armazena dados para a recolha denominada `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>Contadores de desempenho do Replicador transacional

O tempo de execução dos Serviços Fiáveis emite os seguintes eventos na `Service Fabric Transactional Replicator` categoria

 Nome do contador | Descrição |
| --- | --- |
| Iniciar operações txn/seg | O número de novas transações escritas criadas por segundo.|
| Txn Operações/seg | O número de operações de adição/atualização/eliminação efetuadas em coleções fiáveis por segundo.|
| Log Flush Bytes/seg | O número de bytes a ser lavados no disco pelo Replicador Transacional por segundo |
| Operações de aceleração/seg | O número de operações rejeitadas a cada segundo pelo Replicador Transacional devido a estrangulamento. |
| Avg. Transação ms/Commit | Média comete latência por transação em milissegundos |
| Avg. Flush Latência (ms) | Duração média das operações de descarga de disco iniciadas pelo Replicador Transacional em milissegundos |

### <a name="tstore-performance-counters"></a>Balcões de desempenho da TStore

O tempo de execução dos Serviços Fiáveis emite os seguintes eventos na `Service Fabric TStore` categoria

 Nome do contador | Descrição |
| --- | --- |
| Contagem de artigos | O número de artigos na loja.|
| Tamanho do Disco | O tamanho total do disco, em bytes, dos ficheiros de verificação da loja.|
| Checkpoint File Write Bytes/seg | O número de bytes escritos por segundo para o ficheiro de verificação mais recente.|
| Copy Disk Transfer Bytes/seg | O número de bytes de disco lidos (na réplica primária) ou escritos (numa réplica secundária) por segundo durante uma cópia da loja.|

## <a name="next-steps"></a>Passos seguintes
[Fornecedores de EventSource em PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
