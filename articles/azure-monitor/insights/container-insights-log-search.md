---
title: Como consultar registos do Monitor Azure para contentores [ Microsoft Docs
description: O Monitor Azure para contentores recolhe métricas e dados de registo e este artigo descreve os registos e inclui consultas de amostra.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333482"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar registos do Monitor Azure para contentores

O Azure Monitor para contentores recolhe métricas de desempenho, dados de inventário e informações do estado de saúde dos hospedeiros e contentores de contentores, e encaminha-os para o espaço de trabalho log Analytics no Monitor Azure. Os dados são recolhidos a cada três minutos. Estes dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Monitor Azure. Pode aplicar estes dados a cenários que incluem planeamento migratório, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

## <a name="container-records"></a>Registos de contentores

Exemplos de registos recolhidos pelo Azure Monitor para contentores e os tipos de dados que aparecem nos resultados de pesquisa de registo são apresentados na tabela seguinte:

| Tipo de dados | Tipo de dados em Pesquisa de Registo | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | Computador, Nome de Objetos, Contranome &#40;%Tempo de processador, Disco lê MB, Disk Writes MB, Utilização de Memória MB, Bytes de Receção de Rede, Bytes de Envio de Rede, Processador de Utilização,&#41; de rede, ContraValor, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentores | `ContainerInventory` | TimeGenerated, Computer, nome de contentor, nome de contentorhost, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tronco de contentor | `ContainerLog` | TimeGenerated, Computador, ID de imagem, nome do recipiente, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventário do nó de contentores | `ContainerNodeInventory`| TimeGenerated, Computador, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de cápsulas num cluster kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário de nós parte de um cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos Kubernetes | `KubeEvents` | TimeGenerated, Computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Mensagem, SourceSystem | 
| Serviços no cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para nós parte do cluster Kubernetes | Perf &#124; onde objectname == "K8SNode" | Computador, Nome de Objetos, ContraNome &#40;cpuAllocatableBytes, memóriaAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para recipientes parte do cluster Kubernetes | Perf &#124; onde objectname == "K8SContainer" | Contranome &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas Personalizadas |`InsightsMetrics` | Computador, Nome, Espaço de Nome, Origem, SourceSystem, Tags<sup>1,</sup>TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> A propriedade *Tags* representa [múltiplas dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as `InsightsMetrics` métricas recolhidas e armazenadas na tabela e uma descrição das propriedades de registo, consulte a visão geral da [InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Pesquisar registos para analisar dados

Os Registos do Monitor Azure podem ajudá-lo a procurar tendências, diagnosticar estrangulamentos, previsão ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está a funcionar da melhor forma. São fornecidas pesquisas de registo pré-definidas para que comece imediatamente a usar ou a personalizar para devolver a informação da forma que pretende.

Pode efetuar análises interativas de dados no espaço de trabalho selecionando os registos de **eventos View Kubernetes** ou ver a opção de registos de **contentores** no painel de pré-visualização da lista de drop-down da **View in analytics.** A página de Pesquisa de **Registos** aparece à direita da página do portal Azure em que estava.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

A saída de registos do recipiente que é encaminhada para o seu espaço de trabalho são STDOUT e STDERR. Como o Azure Monitor está a monitorizar kubernetes geridos pelo Azure (AKS), o sistema Kube não é recolhido hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo

É muitas vezes útil construir consultas que começam com um exemplo ou dois e depois modificá-las para se adaptarem às suas necessidades. Para ajudar a construir consultas mais avançadas, pode experimentar as seguintes consultas de amostra:

| Consulta | Descrição | 
|-------|-------------|
| Inventário de Contentores<br> &#124; projeto Computador, Nome, Imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; tabela de renderização | Enumerar todas as informações sobre o ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124; onde não (está vazio(Namespace_s))<br> &#124; tipo por TimeGenerated desc<br> &#124; tabela de renderização | Eventos de Kubernetes|
| ArmazémImageInventário<br> &#124; resumir AgregadValue = contagem() por Imagem, ImageTag, Running | Inventário de imagem | 
| **Selecione a opção de exibição de gráfico sinuoso:**<br> Des<br> &#124; onde objectname == "K8SContainer" e ContraNome == "cpuUsageNanoCores" &#124; resumo AvgCPUUsageNanoCores = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | CpU contentor | 
| **Selecione a opção de exibição de gráfico sinuoso:**<br> Des<br> &#124; onde objectname == "K8SContainer" e ContraNome == "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Memória do recipiente |
| InsightsMetrics<br> &#124; onde nome == "requests_count"<br> &#124; resumir Val=any(Val) por TimeGenerated=bin (TimeGenerated, 1m)<br> &#124; tipo por TimeGenerated asc<br> &#124; projeto RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; render barchart  | Pedidos por Minuto com Métricas Personalizadas |

## <a name="query-prometheus-metrics-data"></a>Dados das métricas de Prometheus

O exemplo seguinte é uma consulta de métricas Prometheus que mostra leituras de disco por segundo por disco por nó.

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

Para ver as métricas de Prometheus raspadas pelo Monitor Azure filtradas pelo Namespace, especifique "prometheus". Aqui está uma consulta de amostra para ver `default` as métricas de Prometeu do espaço de nome kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados de Prometheus também podem ser consultados diretamente pelo nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Consulta de config ou erros de raspagem

Para investigar eventuais erros de configuração ou raspagem, `KubeMonAgentEvents` a consulta de exemplo seguinte devolve eventos informais da tabela.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída apresentará resultados semelhantes aos seguintes:

![Resultados de consulta de log de eventos informais do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passos seguintes

O Monitor Azure para contentores não inclui um conjunto predefinido de alertas. Reveja os alertas de [desempenho Create com o Monitor Azure para que](container-insights-alerts.md) os recipientes aprendam a criar alertas recomendados para alta CPU e utilização de memória para suportar os seus DevOps ou processos e procedimentos operacionais. 
