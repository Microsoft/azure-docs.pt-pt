---
title: Como consultar logs de Azure Monitor para contêineres | Microsoft Docs
description: Azure Monitor para contêineres coleta dados de log e métricas e este artigo descreve os registros e inclui exemplos de consultas.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: ae8dd4cccb6795faa02e6705404644f6ccc24864
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948054"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar logs de Azure Monitor para contêineres

Azure Monitor para contêineres coleta métricas de desempenho, dados de inventário e informações de estado de integridade de hosts e contêineres de contêiner e encaminha-os para o espaço de trabalho Log Analytics no Azure Monitor. Dados são recolhidos a cada três minutos. Esses dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Azure monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="container-records"></a>Registos de contentor

Exemplos de registos que são recolhidos pelo Monitor do Azure para contentores e os tipos de dados que aparecem nos resultados de pesquisa de registos são apresentados na tabela a seguir:

| Tipo de dados | Tipo de dados na pesquisa de registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | CounterName de computador, ObjectName, &#40;% de tempo do processador, o disco lê MB, disco escreve MB, MB de utilização de memória, rede receba Bytes, rede enviar Bytes, o processador de seg de utilização, rede&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Registo de contentor | `ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods num cluster do Kubernetes | `KubePodInventory` | TimeGenerated, computador, Clusterid, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerId, Idcontêiner, Name, PodLabel, namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário de parte de nós de um cluster de Kubernetes | `KubeNodeInventory` | TimeGenerated, computador, ClusterName, ClusterId, LastTransitionTimeReady, etiquetas, estado, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents` | TimeGenerated, computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, mensagem, SourceSystem | 
| Serviços no cluster do Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SNode" | Computer, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, myValue, TimeGenerated, caminho, SourceSystem | 
| Métricas de desempenho para a parte de contentores de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas Personalizadas |`InsightsMetrics` | Computador, nome, namespace, origem, SourceSystem, marcas<sup>1</sup>, TimeGenerated, tipo, VA, _ResourceId | 

<sup>1</sup> a propriedade *Tags* representa [várias dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as métricas coletadas e armazenadas na tabela `InsightsMetrics` e uma descrição das propriedades do registro, consulte [visão geral do InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

## <a name="search-logs-to-analyze-data"></a>Registos de pesquisa para analisar dados

Os logs de Azure Monitor podem ajudá-lo a procurar tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está sendo executada de forma ideal. Pesquisas de registos predefinidas são fornecidas por si para começar imediatamente a utilizar ou personalizar para retornar as informações da maneira como desejar.

Pode efetuar análises interativas de dados na área de trabalho ao selecionar o **registos de eventos de Kubernetes do modo de exibição** ou **ver registos de contentor** opção no painel de pré-visualização. O **pesquisa de registos** é apresentada a página à direita da página do portal do Azure que estavam no.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída dos logs de contêiner que é encaminhada para seu espaço de trabalho são STDOUT e STDERR. Porque o Azure Monitor está a monitorizar o Kubernetes gerido pelo Azure (AKS), o sistema do Kube não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo

Muitas vezes é útil criar consultas que começam com um ou dois exemplos em, em seguida, modificá-las para satisfazer as suas necessidades. Para ajudar a criar consultas mais avançadas, pode experimentar com as seguintes consultas de exemplo:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124;Computador, nome, imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime do projeto<br> &#124;tabela de composição | Listar todas as informações de ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124;onde not(isempty(Namespace_s))<br> &#124;Ordenar por TimeGenerated desc<br> &#124;tabela de composição | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124;resumir AggregatedValue = count () by imagem, ImageTag, está em execução | Inventário de imagens | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Contentor da CPU | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Memória de contentor |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;resumir Val = Any (Val) por TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;classificar por TimeGenerated ASC<br> &#124;projeto RequestsPerMinute = Val-anterior (Val), TimeGenerated <br> &#124;renderizar barChart  | Solicitações por minuto com métricas personalizadas |

O exemplo a seguir é uma consulta de métricas Prometheus. As métricas coletadas são contagens e, para determinar quantos erros ocorreram em um período de tempo específico, precisamos subtrair da contagem. O conjunto de registros é particionado por *partitionKey*, ou seja, para cada conjunto exclusivo de *Name*, *hostname*e *OperationType*, executamos uma subconsulta nesse conjunto que ordena os logs pelo *TimeGenerated*, um processo que torna possível Localize o *TimeGenerated* anterior e a contagem registrada para esse horário, para determinar uma taxa.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Passos seguintes

Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps. 
