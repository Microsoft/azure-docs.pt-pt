---
title: Como consultar registos do Monitor Azure para contentores  Microsoft Docs
description: O Monitor Azure para contentores recolhe métricas e dados de registo e este artigo descreve os registos e inclui consultas de amostra.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275363"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar registos do Monitor Azure para contentores

O Azure Monitor para contentores recolhe métricas de desempenho, dados de inventário e informações do estado de saúde dos hospedeiros e contentores de contentores, e encaminha-os para o espaço de trabalho log Analytics no Monitor Azure. Dados são recolhidos a cada três minutos. Estes dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Monitor Azure. Pode aplicar estes dados a cenários que incluem planeamento migratório, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

## <a name="container-records"></a>Registos de contentor

Exemplos de registos que são recolhidos pelo Monitor do Azure para contentores e os tipos de dados que aparecem nos resultados de pesquisa de registos são apresentados na tabela a seguir:

| Tipo de dados | Tipo de dados na pesquisa de registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | CounterName de computador, ObjectName, &#40;% de tempo do processador, o disco lê MB, disco escreve MB, MB de utilização de memória, rede receba Bytes, rede enviar Bytes, o processador de seg de utilização, rede&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Registo de contentor | `ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods num cluster do Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário de parte de nós de um cluster de Kubernetes | `KubeNodeInventory` | TimeGenerated, computador, ClusterName, ClusterId, LastTransitionTimeReady, etiquetas, estado, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents` | TimeGenerated, computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, mensagem, SourceSystem | 
| Serviços no cluster do Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SNode" | Computador, ObjectName, &#40;ContraNome cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch,&#41;CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para a parte de contentores de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SContainer" | Contranome &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores,&#41;memoryRssBytes, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas Personalizadas |`InsightsMetrics` | Computador, Nome, Espaço de Nome, Origem, SourceSystem, Tags<sup>1,</sup>TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> A propriedade *Tags* representa [múltiplas dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as métricas recolhidas e armazenadas na tabela `InsightsMetrics` e uma descrição das propriedades de registo, consulte a visão geral da [InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>O apoio a Prometeu é uma característica na pré-estreia pública neste momento.
>

## <a name="search-logs-to-analyze-data"></a>Registos de pesquisa para analisar dados

Os Registos do Monitor Azure podem ajudá-lo a procurar tendências, diagnosticar estrangulamentos, previsão ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está a funcionar da melhor forma. Pesquisas de registos predefinidas são fornecidas por si para começar imediatamente a utilizar ou personalizar para retornar as informações da maneira como desejar.

Pode efetuar análises interativas de dados no espaço de trabalho selecionando os registos de **eventos View Kubernetes** ou ver a opção de registos de **contentores** no painel de pré-visualização da lista de drop-down da **View in analytics.** A página de Pesquisa de **Registos** aparece à direita da página do portal Azure em que estava.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída de registos do recipiente que é encaminhada para o seu espaço de trabalho são STDOUT e STDERR. Porque o Azure Monitor está a monitorizar o Kubernetes gerido pelo Azure (AKS), o sistema do Kube não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo

Muitas vezes é útil criar consultas que começam com um ou dois exemplos em, em seguida, modificá-las para satisfazer as suas necessidades. Para ajudar a criar consultas mais avançadas, pode experimentar com as seguintes consultas de exemplo:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124;Computador, nome, imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime do projeto<br> &#124;tabela de composição | Listar todas as informações de ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124;onde not(isempty(Namespace_s))<br> &#124;Ordenar por TimeGenerated desc<br> &#124;tabela de composição | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124;resumir AggregatedValue = count () by imagem, ImageTag, está em execução | Inventário de imagens | 
| **Selecione a opção de exibição de gráfico sinuoso:**<br> Des<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Contentor da CPU | 
| **Selecione a opção de exibição de gráfico sinuoso:**<br> Des<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Memória de contentor |
| InsightsMetrics<br> &#124;onde o nome == "requests_count"<br> &#124;resumir Val=any(Val) by TimeGenerated=bin (TimeGenerated, 1m)<br> &#124;classificar por TimeGenerated asc<br> &#124;projeto RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124;render barchart  | Pedidos por Minuto com Métricas Personalizadas |

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

Para ver as métricas de Prometheus raspadas pelo Monitor Azure filtradas pelo Namespace, especifique "prometheus". Aqui está uma consulta de amostra para ver as métricas de Prometeu do espaço de nome de kubernetes `default`.

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

Para investigar eventuais erros de configuração ou raspagem, a consulta de exemplo seguinte devolve eventos informais da tabela `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída apresentará resultados semelhantes aos seguintes:

![Resultados de consulta de log de eventos informais do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passos seguintes

O Monitor Azure para contentores não inclui um conjunto predefinido de alertas. Reveja os alertas de [desempenho Create com o Monitor Azure para que](container-insights-alerts.md) os recipientes aprendam a criar alertas recomendados para alta CPU e utilização de memória para suportar os seus DevOps ou processos e procedimentos operacionais. 
