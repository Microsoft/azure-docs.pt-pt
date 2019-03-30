---
title: Desempenho de recursos de infraestrutura do serviço do Azure monitorização | Documentos da Microsoft
description: Saiba mais sobre contadores de desempenho para monitorização e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ee1608c40801f568b38ace4670b0d5ea7f73003c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663082"
---
# <a name="performance-metrics"></a>Métricas de desempenho

Devem ser recolhidas métricas para compreender o desempenho do seu cluster, bem como as aplicações em execução no mesmo. Para clusters do Service Fabric, recomendamos os seguintes contadores de desempenho a recolher.

## <a name="nodes"></a>Nós

Para as máquinas no seu cluster, considere os seguintes contadores de desempenho para melhor compreender a carga em cada máquina e tomar decisões no dimensionamento do cluster adequado a recolher.

| Categoria do contador | Nome do Contador |
| --- | --- |
| Disco lógico | Espaço livre em disco lógico |
| PhysicalDisk (por disco) | Média Comprimento de fila de leitura do disco |
| PhysicalDisk (por disco) | Média Comprimento de fila de escrita de disco |
| PhysicalDisk (por disco) | Média Disco seg/leitura |
| PhysicalDisk (por disco) | Média Disco seg/escritas |
| PhysicalDisk (por disco) | Leituras de disco/seg |
| PhysicalDisk (por disco) | Bytes Lidos de Disco/seg |
| PhysicalDisk (por disco) | Escritas de disco/seg |
| PhysicalDisk (por disco) | Bytes Escritos em Disco/seg |
| Memória | MBytes disponíveis |
| PagingFile | % De utilização |
| Processor(total) | % Tempo do processador |
| Processo (por serviço) | % Tempo do processador |
| Processo (por serviço) | Identificador de processo |
| Processo (por serviço) | Bytes Privados |
| Processo (por serviço) | Número de Threads |
| Processo (por serviço) | Bytes virtuais |
| Processo (por serviço) | Conjunto de trabalho |
| Processo (por serviço) | Conjunto de trabalho - privado |
| Interface(all-instances) de rede | Bytes recd |
| Interface(all-instances) de rede | Bytes enviados |
| Interface(all-instances) de rede | Total de bytes |
| Interface(all-instances) de rede | Comprimento da fila de saída |
| Interface(all-instances) de rede | Pacotes de saída rejeitados |
| Interface(all-instances) de rede | Pacotes receberam rejeitados |
| Interface(all-instances) de rede | Erros de saída de pacotes |
| Interface(all-instances) de rede | Erros de pacotes recebidos |

## <a name="net-applications-and-services"></a>Aplicações e serviços .NET

Recolha os contadores a seguir se estiver a implementar serviços .NET ao seu cluster. 

| Categoria do contador | Nome do Contador |
| --- | --- |
| Memória de .NET CLR (por serviço) | ID de Processo |
| Memória de .NET CLR (por serviço) | # Total committed Bytes |
| Memória de .NET CLR (por serviço) | # Total reservado Bytes |
| Memória de .NET CLR (por serviço) | # Bytes in all Heaps |
| Memória de .NET CLR (por serviço) | Tamanho de Heap de objeto grande |
| Memória de .NET CLR (por serviço) | # Identificadores de GC |
| Memória de .NET CLR (por serviço) | # Gen 0 Collections |
| Memória de .NET CLR (por serviço) | # Gen 1 Collections |
| Memória de .NET CLR (por serviço) | # Gen 2 coleções |
| Memória de .NET CLR (por serviço) | % De tempo na GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contadores de desempenho personalizados do Service Fabric

Service Fabric gera uma quantidade substancial de contadores de desempenho personalizados. Se tiver o SDK instalado, pode ver a lista abrangente no seu computador Windows em seu aplicativo de Monitor de desempenho (Iniciar > Monitor de desempenho). 

Nas aplicações que está a implementar o cluster, se estiver a utilizar o Reliable Actors, adicionar contadores de `Service Fabric Actor` e `Service Fabric Actor Method` categorias (consulte [diagnóstico do serviço Fabric Reliable Actors](service-fabric-reliable-actors-diagnostics.md)).

Utilizar o Reliable Services ou de comunicação remota do serviço, da mesma forma, temos `Service Fabric Service` e `Service Fabric Service Method` contador categorias que deve recolher contadores de, veja [com a comunicação remota do serviço de monitorização](service-fabric-reliable-serviceremoting-diagnostics.md) e [fiável contadores de desempenho de serviços](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Se utilizar a Reliable Collections, recomendamos que adicione o `Avg. Transaction ms/Commit` partir o `Service Fabric Transactional Replicator` para recolher a latência média de consolidação por métrica de transação.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [geração de eventos ao nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) no Service Fabric
* Recolher métricas de desempenho por meio de [agente do Log Analytics](service-fabric-diagnostics-oms-agent.md)
