---
title: Criar alertas de desempenho para o Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como criar alertas personalizados com base em consultas de registo para memória e utilização de CPU do Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730930"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como posso configurar alertas para problemas de desempenho no Azure Monitor para contentores

O Monitor Azure para contentores monitoriza o desempenho das cargas de trabalho dos contentores que são implantadas para as instâncias de contentores do Azure ou para os aglomerados Kubernetes geridos que estão alojados no Serviço Azure Kubernetes (AKS).

Este artigo descreve como ativar alertas para as seguintes situações:

- Quando a CPU ou a utilização da memória nos nós do cluster excederum limiar
- Quando a CPU ou a utilização da memória em qualquer recipiente dentro de um controlador exceder um limiar em comparação com um limite definido no recurso correspondente
- *NotReady* status nó de estado conta
- *Falhada*, *Pendente,* *Desconhecida,* *Execução*ou *Contagem de pods bem sucedida*
- Quando o espaço livre do disco nos nós do cluster excede um limiar 

Para alertar para uma alta utilização de CPU ou memória, ou um espaço de disco livre baixo nos nós do cluster, utilize as consultas fornecidas para criar um alerta métrico ou um alerta de medição métrica. Os alertas métricos têm menor latência do que os alertas de registo. Mas alertas de registo proporcionam consultas avançadas e maior sofisticação. Os alertas de registo comparam uma data com a presente, utilizando o *operador agora* e recuando uma hora. (Monitor Azure para lojas de contentores todas as datas em formato De Tempo Universal Coordenado (UTC)

Se não estiver familiarizado com os alertas do Monitor Azure, consulte a [visão geral dos alertas no Microsoft Azure](../platform/alerts-overview.md) antes de iniciar. Para saber mais sobre alertas que utilizam consultas de registo, consulte alertas de [registo no Monitor Azure](../platform/alerts-unified-log.md). Para mais informações sobre alertas métricos, consulte [alertas métricos no Monitor Azure](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de registo de utilização de recursos

As consultas nesta secção suportam cada cenário de alerta. São usados no passo 7 da secção de [alerta](#create-an-alert-rule) deste artigo.

A consulta seguinte calcula a utilização média do CPU como uma média da utilização do CPU dos nódosos membros a cada minuto.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

A consulta seguinte calcula a utilização média da memória como uma média da utilização da memória dos nódosos membros a cada minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>As seguintes consultas utilizam \<o espaço reservado que \<valoriza o seu nome de cluster> e o seu nome de controlador> para representar o seu cluster e controlador. Substitua-os por valores específicos do seu ambiente quando configura alertas.

A seguinte consulta calcula a utilização média do CPU de todos os recipientes num controlador como uma média de utilização de CPU de cada instância de contentor num controlador a cada minuto. A medição é uma percentagem do limite fixado para um recipiente.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A seguinte consulta calcula a utilização média da memória de todos os recipientes num controlador como uma média de utilização da memória de cada instância de recipiente num controlador a cada minuto. A medição é uma percentagem do limite fixado para um recipiente.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A seguinte consulta devolve todos os nós e contagens que têm um estatuto de *Ready* e *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A seguinte consulta devolve a fase do casulo conta com base em todas as fases: *Failed*, *Pendente,* *Desconhecido,* *Execução*ou *Sucesso*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Para alertar sobre determinadas fases do pod, tais como *Pendente,* *Falhado,* ou *Desconhecido,* modificar a última linha da consulta. Por exemplo, para alertar sobre a utilização *do Count falhado:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A consulta seguinte devolve os nós de cluster que excedem 90% de espaço livre utilizado. Para obter o ID do cluster, primeiro faça a `ClusterId` seguinte consulta e copie o valor da propriedade:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Siga estes passos para criar um alerta de log no Monitor Azure utilizando uma das regras de pesquisa de registo que foi fornecida anteriormente. Para criar usando um modelo ARM, consulte a criação de [alerta de registo de amostrautilizando o modelo](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)de recurso Azure .

>[!NOTE]
>O procedimento seguinte para criar uma regra de alerta para a utilização do recurso de contentores requer que mude para um novo alerta de registo API, conforme descrito na preferência da [Switch API para alertas](../platform/alerts-log-api-switch.md)de registo .
>

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No portal Azure, procure e selecione espaços de **trabalho Log Analytics**.
3. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que suporta o Monitor Azure para contentores. 
4. No painel do lado esquerdo, selecione **Registos** para abrir a página de registos do Monitor Azure. Usa esta página para escrever e executar consultas de Log Analytics Azure.
5. Na página **De Registos,** colhe uma das [consultas](#resource-utilization-log-search-queries) fornecidas anteriormente no campo de **consulta de pesquisa** e, em seguida, selecione **Executar** para validar os resultados. Se não executar este passo, a opção **de alerta +Nova** não está disponível para selecionar.
6. Selecione **+Novo alerta** para criar um alerta de registo.
7. Na secção **Condição,** selecione a procura de **registo personalizada é \<lógica indefinida>** condição de registo personalizado pré-definida. O tipo de sinal de pesquisa de **registo personalizado** é automaticamente selecionado porque estamos a criar uma regra de alerta diretamente a partir da página de registos do Monitor Azure.  
8. Colhe uma das [consultas](#resource-utilization-log-search-queries) fornecidas anteriormente no campo de **pesquisa.**
9. Configure o alerta da seguinte forma:

    1. Na lista pendente **Com base em**, selecione **Unidades métricas**. Uma medição métrica cria um alerta para cada objeto na consulta que tem um valor acima do nosso limiar especificado.
    1. Para **condição**, selecione **Maior do que**, e introduza **75** como um **limiar** de base inicial para o CPU e alertas de utilização da memória. Para o alerta de espaço de disco baixo, introduza **90**. Ou insira um valor diferente que satisfaça os seus critérios.
    1. Na secção **'Alerta de gatilho baseado na** secção, selecione **infrações consecutivas**. A partir da lista de lançamentos, selecione **Maior do que**, e introduza **2**.
    1. Para configurar um alerta para cpU de contentor ou utilização da memória, em **agregado em**, selecione **ContainerName**. Para configurar para o alerta de disco baixo do nó de cluster, selecione **ClusterId**.
    1. No **Avaliado com base na** secção, detete o valor do **período** para **60 minutos**. A regra será executada a cada 5 minutos e devolverá os registos que foram criados dentro da última hora a partir do tempo atual. Definir o período de tempo para uma ampla janela explica para possíveis latências de dados. Também garante que a consulta devolve dados para evitar um falso negativo em que o alerta nunca dispara.

10. Selecione **Feito** para completar a regra de alerta.
11. Insira um nome no campo de **nome** sintetizador. Especifique uma **Descrição** que forneça detalhes sobre o alerta. E selecione um nível de gravidade adequado a partir das opções fornecidas.
12. Para ativar imediatamente a regra de alerta, aceite o valor predefinido para **a regra Enable sobre a criação**.
13. Selecione um Grupo de **Ação** existente ou crie um novo grupo. Este passo assegura que sejam tomadas as mesmas ações sempre que um alerta é desencadeado. Configure com base na forma como a sua equipa de operações de TI ou DevOps gere incidentes.
14. Selecione **Criar a regra** de alerta para completar a regra de alerta. Começa imediatamente a executar.

## <a name="next-steps"></a>Passos seguintes

- Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de log para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.
- Para saber mais sobre o Monitor Azure e como monitorizar outros aspetos do seu cluster Kubernetes, consulte o desempenho do [cluster View Kubernetes](container-insights-analyze.md) e veja a saúde do [cluster Da Kubernetes.](container-insights-health.md)
