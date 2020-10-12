---
title: Monitorização de desempenho do tecido de serviço Azure
description: Saiba mais sobre os contadores de desempenho para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75464712"
---
# <a name="performance-metrics"></a>Métricas de desempenho

As métricas devem ser recolhidas para entender o desempenho do seu cluster, bem como as aplicações que nele estão a decorrer. Para clusters de tecido de serviço, recomendamos a recolha dos seguintes contadores de desempenho.

## <a name="nodes"></a>Nós

Para as máquinas do seu cluster, considere recolher os seguintes contadores de desempenho para entender melhor a carga em cada máquina e tomar as decisões adequadas de dimensionamento do cluster.

| Categoria de Contador | Nome do Contador |
| --- | --- |
| Disco lógico | Espaço livre de disco lógico |
| PhysicalDisk(por Disco) | Avg. Disco ler comprimento da fila |
| PhysicalDisk(por Disco) | Avg. Disco escrever comprimento da fila |
| PhysicalDisk(por Disco) | Avg. Disco seg/Ler |
| PhysicalDisk(por Disco) | Avg. Disco seg/Write |
| PhysicalDisk(por Disco) | Leituras/seg de disco |
| PhysicalDisk(por Disco) | Discos ler Bytes/seg |
| PhysicalDisk(por Disco) | Escritas/seg de disco |
| PhysicalDisk(por Disco) | Discos De Escrita Bytes/seg |
| Memória | MBytes Disponíveis |
| PagingFile | % Utilização |
| Processador (Total) | % de Tempo do Processador |
| Processo (por serviço) | % de Tempo do Processador |
| Processo (por serviço) | Processo de ID |
| Processo (por serviço) | Bytes privados |
| Processo (por serviço) | Contagem de fios |
| Processo (por serviço) | Bytes Virtuais |
| Processo (por serviço) | Conjunto de Trabalho |
| Processo (por serviço) | Conjunto de trabalho - Privado |
| Interface de rede (todos os casos) | Bytes recd |
| Interface de rede (todos os casos) | Bytes enviados |
| Interface de rede (todos os casos) | Bytes total |
| Interface de rede (todos os casos) | Comprimento da fila de saída |
| Interface de rede (todos os casos) | Pacotes Outbound Descartados |
| Interface de rede (todos os casos) | Pacotes recebidos descartados |
| Interface de rede (todos os casos) | Pacotes Erros de saída |
| Interface de rede (todos os casos) | Pacotes recebidos erros |

## <a name="net-applications-and-services"></a>.NET aplicações e serviços

Recolha os seguintes contadores se estiver a implantar serviços .NET no seu cluster. 

| Categoria de Contador | Nome do Contador |
| --- | --- |
| .NET MEMÓRIA CLR (por serviço) | ID de Processo |
| .NET MEMÓRIA CLR (por serviço) | Total comprometido Bytes |
| .NET MEMÓRIA CLR (por serviço) | Total reservado Bytes |
| .NET MEMÓRIA CLR (por serviço) | Bytes em todos os montes |
| .NET MEMÓRIA CLR (por serviço) | Tamanho da pilha de objeto grande |
| .NET MEMÓRIA CLR (por serviço) | # Alças GC |
| .NET MEMÓRIA CLR (por serviço) | # Coleções Gen 0 |
| .NET MEMÓRIA CLR (por serviço) | # Coleções Gen 1 |
| .NET MEMÓRIA CLR (por serviço) | # Coleções Gen 2 |
| .NET MEMÓRIA CLR (por serviço) | % Tempo em GC |

### <a name="service-fabrics-custom-performance-counters"></a>Balcões de desempenho personalizados da Service Fabric

O Tecido de Serviço gera uma quantidade substancial de contadores de desempenho personalizados. Se tiver o SDK instalado, pode ver a lista completa na sua máquina Windows na sua aplicação Monitor de Desempenho (Iniciar > Monitor de Desempenho). 

Nas aplicações que está a implementar no seu cluster, se estiver a utilizar atores fidedigdos, adicione contadores de `Service Fabric Actor` e `Service Fabric Actor Method` categorias (ver [Service Fabric Reliable Actors Diagnostics).](service-fabric-reliable-actors-diagnostics.md)

Se utilizar serviços fiáveis ou remoting de serviços, `Service Fabric Service` temos e `Service Fabric Service Method` contra-categorias de que deve recolher balcões, ver [monitorização com remoing de serviços](service-fabric-reliable-serviceremoting-diagnostics.md) e [balcões de desempenho de serviços confiáveis](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Se utilizar Coleções Fiáveis, recomendamos adicionar `Avg. Transaction ms/Commit` o do para recolher a `Service Fabric Transactional Replicator` latência média de efetivo por métrica de transação.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a geração de eventos ao nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) no Service Fabric
* Recolher métricas de desempenho através do [agente Log Analytics](service-fabric-diagnostics-oms-agent.md)
