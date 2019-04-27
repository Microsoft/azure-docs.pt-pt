---
title: Como registos de consulta do Azure Monitor para contentores | Documentos da Microsoft
description: Monitor do Azure para contentores recolhe dados de métricas e registo e este artigo descreve os registos e inclui exemplos de consultas.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494784"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar os registos do Azure Monitor para contentores
Monitor do Azure para contentores recolhe métricas de desempenho, dados de inventário e informações de estado de funcionamento dos anfitriões de contentor e contentores e encaminhá-la para a área de trabalho do Log Analytics no Azure Monitor. Dados são recolhidos a cada três minutos. Estes dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Azure Monitor. Pode aplicar esses dados para cenários que incluem planos de migração, análise de capacidade, deteção e resolução de problemas de desempenho a pedido.

## <a name="container-records"></a>Registos de contentor

Exemplos de registos que são recolhidos pelo Monitor do Azure para contentores e os tipos de dados que aparecem nos resultados de pesquisa de registos são apresentados na tabela a seguir:

| Tipo de dados | Tipo de dados na pesquisa de registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | CounterName de computador, ObjectName, &#40;% de tempo do processador, o disco lê MB, disco escreve MB, MB de utilização de memória, rede receba Bytes, rede enviar Bytes, o processador de seg de utilização, rede&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Registo de contentor | `ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods num cluster do Kubernetes | `KubePodInventory` | TimeGenerated, computador, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ID do contentor, ContainerName, nome, PodLabel, espaço de nomes, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário de parte de nós de um cluster de Kubernetes | `KubeNodeInventory` | TimeGenerated, computador, ClusterName, ClusterId, LastTransitionTimeReady, etiquetas, estado, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents` | TimeGenerated, computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, mensagem, SourceSystem | 
| Serviços no cluster do Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SNode" | CounterName de computador, ObjectName, &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para a parte de contentores de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Registos de pesquisa para analisar dados
Registos de Monitor do Azure pode ajudá-lo a analisar as tendências, diagnosticar afunilamentos, previsão, ou correlacionar dados que podem ajudar a determinam se a configuração de cluster atual é satisfatória. Pesquisas de registos predefinidas são fornecidas por si para começar imediatamente a utilizar ou personalizar para retornar as informações da maneira como desejar. 

Pode efetuar análises interativas de dados na área de trabalho ao selecionar o **registos de eventos de Kubernetes do modo de exibição** ou **ver registos de contentor** opção no painel de pré-visualização. O **pesquisa de registos** é apresentada a página à direita da página do portal do Azure que estavam no.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída de registos de contentor é reencaminhada para a área de trabalho são STDOUT e STDERR. Porque o Azure Monitor está a monitorizar o Kubernetes gerido pelo Azure (AKS), o sistema do Kube não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo
Muitas vezes é útil criar consultas que começam com um ou dois exemplos em, em seguida, modificá-las para satisfazer as suas necessidades. Para ajudar a criar consultas mais avançadas, pode experimentar com as seguintes consultas de exemplo:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124;Computador, nome, imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime do projeto<br> &#124;tabela de composição | Listar todas as informações de ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124;onde not(isempty(Namespace_s))<br> &#124;Ordenar por TimeGenerated desc<br> &#124;tabela de composição | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124;resumir AggregatedValue = count () by imagem, ImageTag, está em execução | Inventário de imagens | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Contentor da CPU | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Memória de contentor |

## <a name="next-steps"></a>Passos Seguintes
Monitor do Azure para contentores não inclui um conjunto predefinido de alertas. Reveja os [criar alertas de desempenho com o Azure Monitor para contentores](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória suportar o DevOps ou processos operacionais e procedimentos. 