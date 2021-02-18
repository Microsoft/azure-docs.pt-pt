---
title: Como consultar registos do Azure Monitor para contentores | Microsoft Docs
description: O Azure Monitor para contentores recolhe métricas e dados de registo e este artigo descreve os registos e inclui consultas de amostra.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 8f02d900ba931768c7f8acebc1b124aff777da18
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620032"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar registos do Azure Monitor para contentores

O Azure Monitor para contentores recolhe métricas de desempenho, dados de inventário e informações do estado de saúde dos anfitriões e contentores dos contentores. Os dados são recolhidos a cada três minutos e encaminhados para o espaço de trabalho Log Analytics no Azure Monitor. Estes dados estão disponíveis para [consulta](../log-query/log-query-overview.md) no Azure Monitor. Pode aplicar estes dados em cenários que incluem planeamento de migração, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

## <a name="container-records"></a>Registos de contentores

No quadro seguinte, são fornecidos pormenores sobre os registos recolhidos pelo Azure Monitor para os contentores. Para obter uma listagem das descrições da coluna, consulte a referência para as tabelas [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) e [ContainerLog.](/azure/azure-monitor/reference/tables/containerlog)

| Dados | Origem de dados | Tipo de dados | Campos |
|------|-------------|-----------|--------|
| Inventário de contentores | Kubelet | `ContainerInventory` | TimeGenerated, Computador, Nome, ContentorHostname, Imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tronco de contentor | Docker | `ContainerLog` | TimeGenerated, Computador, ID de imagem, Nome, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventário do nó do contentor | Kube API | `ContainerNodeInventory`| TempoGertado, Computador, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de cápsulas num aglomerado de Kubernetes | Kube API | `KubePodInventory` | TimeGenerated, Computador, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, |
| Inventário de nó de nó parte de um cluster Kubernetes | Kube API | `KubeNodeInventory` | TimeGenerated, Computador, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos do Kubernetes | Kube API | `KubeEvents` | TempoGertado, Computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Mensagem, SourceSystem | 
| Serviços no cluster Kubernetes | Kube API | `KubeServices` | TempoGerado, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para nópartes parte do cluster Kubernetes | As métricas de utilização são obtidas a partir do cAdvisor e os limites da Kube api | Perf &#124; onde ObjectName == "K8SNode" | Computador, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para contentores parte do cluster Kubernetes | As métricas de utilização são obtidas a partir do cAdvisor e os limites da Kube api | Perf &#124; onde ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas Personalizadas ||`InsightsMetrics` | Computador, Nome, Espaço de Nome, Origem, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> A propriedade *Tags* representa [múltiplas dimensões](../essentials/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter mais informações sobre as métricas recolhidas e armazenadas na `InsightsMetrics` tabela e uma descrição das propriedades do registo, consulte a visão geral da [InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Pesquisar registos para analisar dados

Os Registos do Monitor Azure podem ajudá-lo a procurar tendências, diagnosticar estrangulamentos, previsão ou correlacionar dados que possam ajudá-lo a determinar se a configuração atual do cluster está a funcionar da melhor forma. São fornecidas pesquisas de registo pré-definidas para que comece imediatamente a usar ou personalize para devolver a informação da forma que pretende.

Pode efetuar uma análise interativa de dados no espaço de trabalho selecionando os registos de **eventos View Kubernetes** ou ver a opção **de registos** de contentores no painel de pré-visualização a partir da lista de drop-down **analíticos.** A página **'Registar'** aparece à direita da página do portal Azure em que estava.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Os registos de contentores que são reencaminhados para o seu espaço de trabalho são STDOUT e STDERR. Como o Azure Monitor está a monitorizar kubernetes geridos pelo Azure (AKS), o sistema Kube não é recolhido hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo

Muitas vezes é útil construir consultas que começam com um ou dois exemplos e depois modificá-las para se adequar às suas necessidades. Para ajudar a construir consultas mais avançadas, pode experimentar as seguintes consultas de amostra:

| Consulta | Description | 
|-------|-------------|
| ContentorInventory<br> &#124; projeto Computador, Nome, Imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; mesa de renderização | Listar todas as informações do ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124; em que não(isempty(Namespace_s))<br> &#124; tipo por Desc degenerado Tempo<br> &#124; mesa de renderização | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = contagem() por Imagem, ImageTag, Running | Inventário de imagem | 
| **Selecione a opção de visualização do gráfico de linha**:<br> Des<br> &#124; onde ObjectName == "K8SContainer" e CounterName == "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) by bin (TimeGenerated, 30m), InstanceName | CPU de contentores | 
| **Selecione a opção de visualização do gráfico de linha**:<br> Des<br> &#124; onde ObjectName == "K8SContainer" e CounterName == "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg (CounterValue) by bin (TimeGenerated, 30m), InstanceName | Memória do recipiente |
| InsightsMetrics<br> &#124; onde o nome == "requests_count"<br> &#124; resumir Val=any(Val) por TimeGenerated=bin (TimeGenerated, 1m)<br> &#124; tipo por TimeGenerated asc<br> &#124; projeto RequestsPerMinute = Val - prev (Val), TimeGenerated <br> &#124; render barchart  | Pedidos por minuto com métricas personalizadas |

## <a name="query-prometheus-metrics-data"></a>Dados de métricas de Consulta Prometheus

O exemplo a seguir é uma consulta de métricas prometheus que mostra as leituras do disco por segundo por disco por nó.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Para visualizar métricas prometheus raspadas pelo Monitor Azure filtrados pelo Namespace, especifique "prometheus". Aqui está uma consulta de amostra para ver métricas de Prometeu do `default` espaço de nomes kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados prometheus também podem ser consultados diretamente pelo nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Erros de consulta ou raspagem

Para investigar qualquer erro de configuração ou raspagem, a seguinte consulta de exemplo retorna eventos informativos da `KubeMonAgentEvents` tabela.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída apresenta resultados semelhantes ao seguinte exemplo:

![Registar resultados de eventos informativos do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passos seguintes

O Monitor Azure para contentores não inclui um conjunto de alertas predefinidos. Reveja os [alertas de desempenho da Create com o Azure Monitor para que os recipientes](./container-insights-log-alerts.md) aprendam a criar alertas recomendados para alta utilização de CPU e memória para suportar os seus DevOps ou processos e procedimentos operacionais.
