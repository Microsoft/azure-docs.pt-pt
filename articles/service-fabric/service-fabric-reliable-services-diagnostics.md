---
title: Diagnóstico de Reliable Services do Service Fabric com monitorização de estado do Azure | Documentos da Microsoft
description: Funcionalidade de diagnóstico para Stateful Reliable Services no Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f9ec807f951bbaec0be3f1729ba2a7246ef37c62
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738033"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico dos Reliable Services com Monitorização de Estado
A classe de Azure Service Fabric com estado fiável serviços StatefulServiceBase emite [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos que podem ser usados para depurar o serviço, fornecer informações sobre como o tempo de execução está a funcionar e ajudar a resolver problemas.

## <a name="eventsource-events"></a>Eventos de EventSource
O nome de EventSource para a classe StatefulServiceBase de serviços fiáveis com monitoração de estado é "Microsoft-ServiceFabric-serviços". Eventos a partir desta origem de eventos são apresentados no [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) janela quando o serviço está a ser [depurado no Visual Studio](service-fabric-debugging-your-application.md).

São exemplos de ferramentas e tecnologias que ajudam na recolha de e/ou ver eventos de EventSource [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)e o [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID do Evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitida quando a tarefa de RunAsync do serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitida quando a tarefa de RunAsync do serviço é cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitida quando a tarefa de RunAsync do serviço estiver concluída |
| StatefulRunAsyncSlowCancellation |4 |Aviso |Emitida quando a tarefa de RunAsync do serviço demora demasiado tempo a concluir a cancelamento |
| StatefulRunAsyncFailure |5 |Erro |Emitida quando a tarefa de RunAsync serviço lança uma exceção |

## <a name="interpret-events"></a>Interpretar os eventos
Os eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o escritor do serviço para compreender o ciclo de vida de um serviço, bem como o período de tempo para quando um serviço é iniciado, cancela ou é concluída. Estas informações podem ser úteis quando depurar problemas de serviço ou compreender o ciclo de vida do serviço.

Criadores de serviços devem prestar muita atenção aos eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitida sempre que a tarefa de RunAsync() serviço lança uma exceção. Normalmente, uma exceção lançada indica um erro ou o bug no serviço. Além disso, a exceção faz com que o serviço falhe, para que ele é movido para um nó diferente. Esta operação pode ser cara e pode atrasar a solicitações de entrada enquanto o serviço é movido. Gravadores de serviço devem determinar a causa da exceção e, se possível, atenuá-lo.

StatefulRunAsyncSlowCancellation é emitida sempre que uma solicitação de cancelamento da tarefa de RunAsync demora mais tempo do que quatro segundos. Quando um serviço demora demasiado tempo a concluir o cancelamento, ela afeta a capacidade do serviço seja reiniciado rapidamente noutro nó. Este cenário poderá afetar a disponibilidade geral do serviço.

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução do Reliable Services define as categorias de contador de desempenho seguintes:

| Categoria | Descrição |
| --- | --- |
| Replicador Transacional do Service Fabric |Contadores específicos do replicador transacional do Azure Service Fabric |
| TStore do Service Fabric |Contadores específicos de tstore do Azure Service Fabric |

Replicador transacional do Service Fabric é utilizado pela [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) replicar transações dentro de um determinado conjunto de [réplicas](service-fabric-concepts-replica-lifecycle.md).

O tstore do Service Fabric é um componente utilizado no [Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) para armazenar e obter pares chave-valor.

O [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) aplicativo que está disponível por predefinição no sistema operacional Windows pode ser usado para recolher e visualizar dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instâncias do contador de desempenho
Um cluster com um grande número de serviços fiáveis ou partições de reliable Services terá um grande número de instâncias de contadores de desempenho do replicador transacional. Isso também é o caso dos contadores de desempenho do TStore, mas também é multiplicado pelo número de dicionários fiável e fiável filas utilizados. Os nomes das instâncias do contador de desempenho podem ajudar a identificar as específicas [partição](service-fabric-concepts-partitioning.md), réplica de serviço e o fornecedor de estado no caso do tstore do que a instância do contador de desempenho está associada.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoria do replicador transacional do Service Fabric
Para a categoria `Service Fabric Transactional Replicator`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada por meio [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado uma determinada réplica de um serviço fiável. ID de réplica está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outras instâncias do contador de desempenho geradas pela mesma partição. Podem encontrar mais detalhes sobre as réplicas e a respetiva função no reliable services [aqui](service-fabric-concepts-replica-lifecycle.md).

O nome de instância do contador seguinte é típico de um contador sob o `Service Fabric Transactional Replicator` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres de ID de partição Service Fabric, e `131652217797162571` é o ID de réplica.

#### <a name="service-fabric-tstore-category"></a>Categoria do tstore do Service Fabric
Para a categoria `Service Fabric TStore`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* é a representação de cadeia de caracteres de ID de partição o Service Fabric que está associada à instância do contador de desempenho. O ID de partição é um GUID e sua representação de cadeia de caracteres é gerada por meio [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) especificador de formato "D".

*ServiceFabricReplicaId* é o ID associado uma determinada réplica de um serviço fiável. ID de réplica está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outras instâncias do contador de desempenho geradas pela mesma partição. Podem encontrar mais detalhes sobre as réplicas e a respetiva função no reliable services [aqui](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* é o ID associado um fornecedor de estado dentro de um serviço fiável. ID do fornecedor de estado está incluído no nome de instância do contador de desempenho para diferenciar um tstore dos outros.

*PerformanceCounterInstanceDifferentiator* é um ID de diferenciação associado a uma instância do contador de desempenho dentro de um fornecedor de estado. Esse diferenciador está incluído no nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outras instâncias do contador de desempenho geradas pelo mesmo provedor de estado.

O nome de instância do contador seguinte é típico de um contador sob o `Service Fabric TStore` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres de ID de partição Service Fabric, `131652217797162571` é o ID de réplica `142652217797162571` é o ID do fornecedor de estado, e `1337` é o diferenciador de instância do contador de desempenho.

### <a name="transactional-replicator-performance-counters"></a>Contadores de desempenho do replicador transacionais

O tempo de execução do Reliable Services emite os seguintes eventos sob a `Service Fabric Transactional Replicator` categoria

 Nome do contador | Descrição |
| --- | --- |
| Início de Operações de Transações/segundo | O número de novas transações de escrita criadas por segundo.|
| Operações de Transações/segundo | O número de operações de adicionar/atualizar/eliminar executadas em coleções fiáveis por segundo.|
| Média Esvaziar latência (ms) | O número de bytes a libertar no disco pelo replicador transacional por segundo |
| Operações Limitadas/segundo | O número de operações rejeitadas a cada segundo pelo replicador transacional devido à limitação. |
| Média Transação ms/consolidação | Latência média de consolidação por transação em milissegundos |
| Média Esvaziar latência (ms) | Duração média de operações de descarregamento de disco iniciadas pelo replicador transacional em milissegundos |

### <a name="tstore-performance-counters"></a>Contadores de desempenho do TStore

O tempo de execução do Reliable Services emite os seguintes eventos sob a `Service Fabric TStore` categoria

 Nome do contador | Descrição |
| --- | --- |
| Número de Itens | O número de itens no arquivo.|
| Tamanho do Disco | O tamanho total do disco, em bytes, dos ficheiros de ponto de verificação para o arquivo.|
| Bytes/segundo de Escrita de Ficheiros de Ponto de Verificação | O número de bytes escritos por segundo para o ficheiro de ponto de verificação mais recente.|
| Bytes/Segundo de Transferência de Disco da Cópia | O número de bytes de disco lidos (na réplica primária) ou nele gravados (numa réplica secundária) por segundo durante uma cópia do arquivo.|

## <a name="next-steps"></a>Passos Seguintes
[Fornecedores de EventSource na PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
