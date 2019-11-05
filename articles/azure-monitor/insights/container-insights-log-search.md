---
title: Como consultar logs de Azure Monitor para contêineres | Microsoft Docs
description: Azure Monitor para contêineres coleta dados de log e métricas e este artigo descreve os registros e inclui exemplos de consultas.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 787e9e6d0ae86568e1af74b4d67fb716841a02df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477097"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar logs de Azure Monitor para contêineres

Azure Monitor para contêineres coleta métricas de desempenho, dados de inventário e informações de estado de integridade de hosts e contêineres de contêiner e encaminha-os para o espaço de trabalho Log Analytics no Azure Monitor. Os dados são coletados a cada três minutos. Esses dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Azure monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="container-records"></a>Registros de contêiner

Exemplos de registros que são coletados por Azure Monitor para contêineres e os tipos de dados que aparecem nos resultados da pesquisa de log são exibidos na tabela a seguir:

| Data type | Tipo de dados na pesquisa de logs | Campos |
| --- | --- | --- |
| Desempenho para hosts e contêineres | `Perf` | Computador, ObjectName, CounterName &#40;% de tempo do processador, leituras de disco MB, gravações em disco MB, uso de memória MB, bytes de recebimento de rede, bytes de&#41;envio de rede, uso de processador s, rede, valor de intervalo, tempo gerado, caminho, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, computador, nome do contêiner, ContainerHostname, imagem, ImageTag, Contêinerstate, ExitCode, EnvironmentVar, comando, Createdtime, preparoutime, Finalizatime, SourceSystem, Idcontêiner, Imageid |
| Log de contêiner | `ContainerLog` | TimeGenerated, Computer, ID da imagem, nome do contêiner, LogEntry, LogEntry, SourceSystem, ContainerId |
| Inventário de nó de contêiner | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods em um cluster kubernetes | `KubePodInventory` | TimeGenerated, computador, Clusterid, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerId, Idcontêiner, Name, PodLabel, namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário de nós que fazem parte de um cluster kubernetes | `KubeNodeInventory` | TimeGenerated, computador, ClusterName, Clusterid, LastTransitionTimeReady, rótulos, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, SourceSystem | 
| Serviços no cluster kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para nós que fazem parte do cluster kubernetes | Perf &#124; , em que ObjectName = = "K8SNode" | Computer, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, myValue, TimeGenerated, caminho, SourceSystem | 
| Métricas de desempenho para parte de contêineres do cluster kubernetes | Perf &#124; , em que ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas Personalizadas |`InsightsMetrics` | Computador, nome, namespace, origem, SourceSystem, marcas<sup>1</sup>, TimeGenerated, tipo, VA, _ResourceId | 

<sup>1</sup> a propriedade *Tags* representa [várias dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as métricas coletadas e armazenadas na tabela `InsightsMetrics` e uma descrição das propriedades do registro, consulte [visão geral do InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

## <a name="search-logs-to-analyze-data"></a>Pesquisar logs para analisar dados

Os logs de Azure Monitor podem ajudá-lo a procurar tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está sendo executada de forma ideal. As pesquisas de log predefinidas são fornecidas para que você comece imediatamente a usar ou a Personalizar para retornar as informações da maneira desejada.

Você pode executar a análise interativa de dados no espaço de trabalho selecionando a opção **Exibir logs de eventos kubernetes** ou **Exibir logs de contêiner** no painel de visualização na lista suspensa **exibição na análise** . A página **pesquisa de logs** é exibida à direita da página de portal do Azure na qual você estava.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída dos logs de contêiner que é encaminhada para seu espaço de trabalho são STDOUT e STDERR. Como Azure Monitor está monitorando kubernetes (AKS) gerenciados pelo Azure, o Kube-System não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log

Geralmente, é útil criar consultas que começam com um exemplo ou duas e, em seguida, modificá-las de acordo com suas necessidades. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de exemplo:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124;Computador do projeto, nome, imagem, ImageTag, Contêinerstate, renomear, Iniciadotime, Concluídotime<br> &#124;renderizar tabela | Listar todas as informações do ciclo de vida de um contêiner| 
| KubeEvents_CL<br> &#124;onde não (IsEmpty (Namespace_s))<br> &#124;classificar por TimeGenerated desc<br> &#124;renderizar tabela | Eventos de kubernetes|
| ContainerImageInventory<br> &#124;resumir AggregatedValue = Count () por Image, ImageTag, running | Inventário de imagens | 
| **Selecione a opção de exibição de gráfico de linhas**:<br> Perf<br> &#124;Where ObjectName = = "K8SContainer" e CounterName = = "cpuUsageNanoCores" &#124; resume AvgCPUUsageNanoCores = AVG (comvalue) por bin (TimeGenerated, 30 m), InstanceName | CPU do contêiner | 
| **Selecione a opção de exibição de gráfico de linhas**:<br> Perf<br> &#124;Where ObjectName = = "K8SContainer" e CounterName = = "memoryRssBytes" &#124; resume AvgUsedRssMemoryBytes = AVG (comvalue) por bin (TimeGenerated, 30 m), InstanceName | Memória do contentor |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;resumir Val = Any (Val) por TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;classificar por TimeGenerated ASC<br> &#124;projeto RequestsPerMinute = Val-anterior (Val), TimeGenerated <br> &#124;renderizar barChart  | Solicitações por minuto com métricas personalizadas |

## <a name="query-prometheus-metrics-data"></a>Consultar dados de métricas do Prometheus

O exemplo a seguir é uma consulta de métricas Prometheus mostrando leituras de disco por segundo por disco por nó.

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

Para exibir as métricas de Prometheus recortadas por Azure Monitor filtradas pelo namespace, especifique "Prometheus". Aqui está uma consulta de exemplo para exibir as métricas de Prometheus do namespace do `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados de Prometheus também podem ser consultados diretamente por nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Erros de configuração de consulta ou de sucata

Para investigar qualquer configuração ou erros de recorte, a consulta de exemplo a seguir retorna eventos informativos da tabela `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída mostrará resultados semelhantes ao seguinte:

![Registrar resultados de consulta de eventos informativos do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Passos seguintes

Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps. 
