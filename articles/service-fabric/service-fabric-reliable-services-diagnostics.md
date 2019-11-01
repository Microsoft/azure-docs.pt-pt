---
title: Diagnóstico de Reliable Services com estado do Azure Service Fabric | Microsoft Docs
description: Funcionalidade de diagnóstico para Reliable Services com estado no Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 50e3368aa8808307fa479a290eaf10ca3f22289d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242869"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico dos Reliable Services com Monitorização de Estado
O Azure Service Fabric com estado Reliable Services classe StatefulServiceBase emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que podem ser usados para depurar o serviço, fornecer informações sobre como o tempo de execução está operando e ajudar na solução de problemas.

## <a name="eventsource-events"></a>Eventos EventSource
O nome EventSource para a classe StatefulServiceBase com estado Reliable Services é "Microsoft-Service Fabric-Services". Os eventos dessa origem do evento aparecem na janela [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou exibir eventos EventSource são [Perfview](https://www.microsoft.com/download/details.aspx?id=28567), [diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)e a [biblioteca do Microsoft TraceEvent](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID do evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitido quando a tarefa RunAsync do serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitido quando a tarefa RunAsync do serviço é cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitido quando a tarefa RunAsync do serviço é concluída |
| Nos |4 |Aviso |Emitido quando a tarefa RunAsync do serviço leva muito tempo para concluir o cancelamento |
| StatefulRunAsyncFailure |5 |Erro |Emitido quando a tarefa RunAsync do serviço gera uma exceção |

## <a name="interpret-events"></a>Interpretar eventos
Os eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o gravador de serviço entender o ciclo de vida de um serviço, bem como o tempo para quando um serviço é iniciado, cancelado ou concluído. Essas informações podem ser úteis na depuração de problemas de serviço ou na compreensão do ciclo de vida do serviço.

Os gravadores de serviço devem prestar muita atenção aos eventos nos e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitido sempre que a tarefa RunAsync () do serviço gera uma exceção. Normalmente, uma exceção lançada indica um erro ou um bug no serviço. Além disso, a exceção faz com que o serviço falhe, portanto, ele é movido para um nó diferente. Essa operação pode ser cara e pode atrasar as solicitações de entrada enquanto o serviço é movido. Os gravadores de serviço devem determinar a causa da exceção e, se possível, atenuá-la.

Nos é emitido sempre que uma solicitação de cancelamento para a tarefa RunAsync demora mais de quatro segundos. Quando um serviço leva muito tempo para concluir o cancelamento, ele afeta a capacidade do serviço de ser reiniciado rapidamente em outro nó. Esse cenário pode afetar a disponibilidade geral do serviço.

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução de Reliable Services define as seguintes categorias de contador de desempenho:

| Categoria | Descrição |
| --- | --- |
| Replicador transacional Service Fabric |Contadores específicos para o replicador transacional Service Fabric do Azure |
| Service Fabric TStore |Contadores específicos para o Azure Service Fabric TStore |

O replicador transacional Service Fabric é usado pelo [Gerenciador de estado confiável](service-fabric-reliable-services-reliable-collections-internals.md) para replicar transações em um determinado conjunto de [réplicas](service-fabric-concepts-replica-lifecycle.md).

O Service Fabric TStore é um componente usado em [coleções confiáveis](service-fabric-reliable-services-reliable-collections-internals.md) para armazenar e recuperar pares chave-valor.

O aplicativo de [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por padrão no sistema operacional Windows pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los em tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instância do contador de desempenho
Um cluster que tem um grande número de Reliable Services ou partições de serviço confiável terá um grande número de instâncias de contador de desempenho do replicador transacional. Esse também é o caso para contadores de desempenho TStore, mas também é multiplicado pelo número de dicionários confiáveis e filas confiáveis usadas. Os nomes de instância do contador de desempenho podem ajudar a identificar a [partição](service-fabric-concepts-partitioning.md)específica, a réplica de serviço e o provedor de estado no caso do TStore, ao qual a instância do contador de desempenho está associada.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric categoria de replicador transacional
Para a categoria `Service Fabric Transactional Replicator`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceFabricReplicaId* é a ID associada a uma determinada réplica de um serviço confiável. A ID da réplica é incluída no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outras instâncias de contador de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e sua função nos Reliable Services podem ser encontrados [aqui](service-fabric-concepts-replica-lifecycle.md).

O nome da instância do contador a seguir é típico para um contador sob a categoria `Service Fabric Transactional Replicator`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres da ID da partição de Service Fabric e `131652217797162571` é a ID da réplica.

#### <a name="service-fabric-tstore-category"></a>Service Fabric categoria TStore
Para a categoria `Service Fabric TStore`, os nomes de instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* é a representação de cadeia de caracteres da ID de partição de Service Fabric à qual a instância do contador de desempenho está associada. A ID de partição é um GUID, e sua representação de cadeia de caracteres é gerada por meio de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

*ServiceFabricReplicaId* é a ID associada a uma determinada réplica de um serviço confiável. A ID da réplica é incluída no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outras instâncias de contador de desempenho geradas pela mesma partição. Mais detalhes sobre réplicas e sua função nos Reliable Services podem ser encontrados [aqui](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* é a ID associada a um provedor de estado em um serviço confiável. A ID do provedor de estado é incluída no nome da instância do contador de desempenho para diferenciar um TStore de outro.

*PerformanceCounterInstanceDifferentiator* é uma ID diferencial associada a uma instância de contador de desempenho dentro de um provedor de estado. Esse diferenciador está incluído no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflitos com outras instâncias de contador de desempenho geradas pelo mesmo provedor de estado.

*StateProviderName* é o nome associado a um provedor de estado em um serviço confiável. O nome do provedor de estado é incluído no nome da instância do contador de desempenho para que os usuários identifiquem facilmente o estado que ele fornece.

O nome da instância do contador a seguir é típico para um contador sob a categoria `Service Fabric TStore`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres da ID da partição Service Fabric, `131652217797162571` é a ID da réplica, `142652217797162571` é a ID do provedor de estado e `1337` é o diferenciador da instância do contador de desempenho. `urn:MyReliableDictionary/dataStore` é o nome do provedor de estado que armazena dados para a coleção chamada `urn:MyReliableDictionary`.

### <a name="transactional-replicator-performance-counters"></a>Contadores de desempenho do replicador transacional

O tempo de execução de Reliable Services emite os seguintes eventos na categoria `Service Fabric Transactional Replicator`

 Nome do contador | Descrição |
| --- | --- |
| Operações de início de TXN/s | O número de novas transações de gravação criadas por segundo.|
| Operações de TXN/s | O número de operações de adição/atualização/exclusão realizadas em coleções confiáveis por segundo.|
| Bytes de liberação de log/s | O número de bytes que estão sendo liberados para o disco pelo replicador transacional por segundo |
| Operações limitadas/s | O número de operações rejeitadas a cada segundo pelo replicador transacional devido à limitação. |
| Média de transações MS/confirmação | Latência média de confirmação por transação em milissegundos |
| Latência média de liberação (MS) | Duração média das operações de liberação de disco iniciadas pelo replicador transacional em milissegundos |

### <a name="tstore-performance-counters"></a>Contadores de desempenho do TStore

O tempo de execução de Reliable Services emite os seguintes eventos na categoria `Service Fabric TStore`

 Nome do contador | Descrição |
| --- | --- |
| Contagem de itens | O número de itens no repositório.|
| Tamanho do Disco | O tamanho total do disco, em bytes, dos arquivos de ponto de verificação para o repositório.|
| Bytes de gravação do arquivo de ponto de verificação/s | O número de bytes gravados por segundo para o arquivo de ponto de verificação mais recente.|
| Bytes de transferência de disco de cópia/s | O número de bytes de disco lidos (na réplica primária) ou gravados (em uma réplica secundária) por segundo durante uma cópia de armazenamento.|

## <a name="next-steps"></a>Passos seguintes
[Provedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
