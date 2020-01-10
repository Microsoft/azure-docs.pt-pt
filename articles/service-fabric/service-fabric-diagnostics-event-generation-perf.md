---
title: Monitoramento de desempenho do Azure Service Fabric
description: Saiba mais sobre contadores de desempenho para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464712"
---
# <a name="performance-metrics"></a>Métricas de desempenho

As métricas devem ser coletadas para entender o desempenho do cluster, bem como os aplicativos em execução. Para Service Fabric clusters, é recomendável coletar os contadores de desempenho a seguir.

## <a name="nodes"></a>Nós

Para os computadores em seu cluster, considere coletar os contadores de desempenho a seguir para entender melhor a carga em cada máquina e tomar decisões de dimensionamento de cluster apropriadas.

| Categoria do contador | Nome do contador |
| --- | --- |
| Disco lógico | Espaço Livre de Disco Lógico |
| PhysicalDisk (por disco) | Comprimento médio da fila de leitura de disco |
| PhysicalDisk (por disco) | Comprimento médio da fila de gravação de disco |
| PhysicalDisk (por disco) | Média de disco s/leitura |
| PhysicalDisk (por disco) | Média de disco s/gravação |
| PhysicalDisk (por disco) | Leituras de disco/seg |
| PhysicalDisk (por disco) | Bytes Lidos de Disco/seg |
| PhysicalDisk (por disco) | Escritas de disco/seg |
| PhysicalDisk (por disco) | Bytes Escritos em Disco/seg |
| Memória | MBytes disponíveis |
| Paginação | % De uso |
| Processador (total) | % Tempo do processador |
| Processo (por serviço) | % Tempo do processador |
| Processo (por serviço) | Processo de ID |
| Processo (por serviço) | Bytes Privados |
| Processo (por serviço) | Número de Threads |
| Processo (por serviço) | Bytes Virtuais |
| Processo (por serviço) | Conjunto de Trabalho |
| Processo (por serviço) | Conjunto de trabalho-privado |
| Interface de rede (todas as instâncias) | Bytes de rECd |
| Interface de rede (todas as instâncias) | Bytes enviados |
| Interface de rede (todas as instâncias) | Total de bytes |
| Interface de rede (todas as instâncias) | Comprimento da fila de saída |
| Interface de rede (todas as instâncias) | Pacotes de saída descartados |
| Interface de rede (todas as instâncias) | Pacotes recebidos descartados |
| Interface de rede (todas as instâncias) | Erros de pacotes de saída |
| Interface de rede (todas as instâncias) | Erros de pacotes recebidos |

## <a name="net-applications-and-services"></a>Aplicativos e serviços .NET

Colete os contadores a seguir se você estiver implantando serviços .NET em seu cluster. 

| Categoria do contador | Nome do contador |
| --- | --- |
| Memória .NET CLR (por serviço) | ID de Processo |
| Memória .NET CLR (por serviço) | N º total de bytes confirmados |
| Memória .NET CLR (por serviço) | N º total de bytes reservados |
| Memória .NET CLR (por serviço) | N º de bytes em todos os heaps |
| Memória .NET CLR (por serviço) | Tamanho do heap de objeto grande |
| Memória .NET CLR (por serviço) | Identificadores # GC |
| Memória .NET CLR (por serviço) | N º de coletas de Gen 0 |
| Memória .NET CLR (por serviço) | N º de coletas de Gen 1 |
| Memória .NET CLR (por serviço) | N º de coletas de Gen 2 |
| Memória .NET CLR (por serviço) | % De tempo no GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contadores de desempenho personalizados do Service Fabric

Service Fabric gera uma quantidade significativa de contadores de desempenho personalizados. Se você tiver o SDK instalado, poderá ver a lista abrangente em seu computador Windows em seu aplicativo monitor de desempenho (Iniciar > Monitor de desempenho). 

Nos aplicativos que você está implantando no cluster, se você estiver usando Reliable Actors, adicione contadores das categorias `Service Fabric Actor` e `Service Fabric Actor Method` (consulte [Service Fabric Reliable Actors Diagnostics](service-fabric-reliable-actors-diagnostics.md)).

Se você usar a comunicação remota do Reliable Services ou do serviço, de forma semelhante, temos `Service Fabric Service` e `Service Fabric Service Method` categorias de contador das quais você deve coletar contadores, consulte monitoramento com contadores de desempenho de [comunicação remota do serviço](service-fabric-reliable-serviceremoting-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Se você usar coleções confiáveis, recomendamos adicionar o `Avg. Transaction ms/Commit` da `Service Fabric Transactional Replicator` para coletar a latência média de confirmação por métrica de transação.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a geração de eventos no nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) no Service Fabric
* Coletar métricas de desempenho por meio do [agente de log Analytics](service-fabric-diagnostics-oms-agent.md)
