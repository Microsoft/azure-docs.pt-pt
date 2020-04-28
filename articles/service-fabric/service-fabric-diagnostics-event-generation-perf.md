---
title: Monitorização do desempenho do tecido do serviço Azure
description: Conheça os contadores de desempenho para monitorização e diagnóstico de clusters Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464712"
---
# <a name="performance-metrics"></a>Métricas de desempenho

As métricas devem ser recolhidas para compreender o desempenho do seu cluster, bem como as aplicações que estão a ser em execução no mesmo. Para os clusters service Fabric, recomendamos a recolha dos seguintes contadores de desempenho.

## <a name="nodes"></a>Nós

Para as máquinas do seu cluster, considere recolher os seguintes contadores de desempenho para entender melhor a carga em cada máquina e tomar decisões adequadas de escala de cluster.

| Categoria Contador | Contra nome |
| --- | --- |
| Disco Lógico | Espaço Livre de Disco Lógico |
| Disco Físico (por Disco) | Avg. Disk Ler comprimento da fila |
| Disco Físico (por Disco) | Avg. Disk Write Queue Comprimento |
| Disco Físico (por Disco) | Avg. Disk sec/Read |
| Disco Físico (por Disco) | Avg. Disk sec/Write |
| Disco Físico (por Disco) | Leituras/seg de disco |
| Disco Físico (por Disco) | Discos Ler Bytes/seg |
| Disco Físico (por Disco) | Escritas de Disco/seg |
| Disco Físico (por Disco) | Discos Write Bytes/seg |
| Memória | MBytes disponíveis |
| Ficheiro paging | Uso % |
| Processador(Total) | % de Tempo do Processador |
| Processo (por serviço) | % de Tempo do Processador |
| Processo (por serviço) | Processo de ID |
| Processo (por serviço) | Bytes Privados |
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
| Interface de rede (todos os casos) | Erros de saída de pacotes |
| Interface de rede (todos os casos) | Pacotes Erros Recebidos |

## <a name="net-applications-and-services"></a>.NET aplicações e serviços

Recolha os seguintes contadores se estiver a implementar serviços .NET para o seu cluster. 

| Categoria Contador | Contra nome |
| --- | --- |
| .NET CLR Memória (por serviço) | ID de Processo |
| .NET CLR Memória (por serviço) | # Total bytes comprometidos |
| .NET CLR Memória (por serviço) | # Total reservado Bytes |
| .NET CLR Memória (por serviço) | Bytes em todos os Montes |
| .NET CLR Memória (por serviço) | Tamanho heap objeto grande |
| .NET CLR Memória (por serviço) | # Pegas GC |
| .NET CLR Memória (por serviço) | # Gen 0 Collections |
| .NET CLR Memória (por serviço) | # Coleções Gen 1 |
| .NET CLR Memória (por serviço) | # Coleções Gen 2 |
| .NET CLR Memória (por serviço) | % Tempo em GC |

### <a name="service-fabrics-custom-performance-counters"></a>Balcões de desempenho personalizados da Fabric de Serviço

O Tecido de Serviço gera uma quantidade substancial de contadores de desempenho personalizados. Se tiver o SDK instalado, pode ver a lista completa da sua máquina Windows na sua aplicação Performance Monitor (Iniciar > Monitor de Desempenho). 

Nas aplicações que está a implementar para o seu cluster, se `Service Fabric Actor` `Service Fabric Actor Method` estiver a utilizar Atores Fiáveis, adicione contadores e categorias (ver [Diagnósticos de Atores Fiáveis](service-fabric-reliable-actors-diagnostics.md)de Tecido de Serviço).

Se utilizar serviços fiáveis ou remoting `Service Fabric Service` de `Service Fabric Service Method` serviço, também temos e contratemos categorias de que deve recolher contadores, ver [monitorização com responsadede serviços](service-fabric-reliable-serviceremoting-diagnostics.md) e balcões de [desempenho de serviços fiáveis.](service-fabric-reliable-services-diagnostics.md#performance-counters) 

Se utilizar Coleções Fiáveis, recomendamos adicionar o `Avg. Transaction ms/Commit` a partir do para `Service Fabric Transactional Replicator` recolher a latência média de compromisso por métrica de transação.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a geração de [eventos ao nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) em Service Fabric
* Recolher métricas de desempenho através do [agente Log Analytics](service-fabric-diagnostics-oms-agent.md)
