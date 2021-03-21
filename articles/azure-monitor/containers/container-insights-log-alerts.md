---
title: Registar alertas a partir de insights de contentores | Microsoft Docs
description: Este artigo descreve como criar alertas de registo personalizados para memória e utilização de CPU a partir de insights de contentores.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 64d499d69194ac338d367ae094e42f4c8af23bef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711200"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Como criar alertas de registo a partir de insights de contentores

Os conhecimentos dos contentores monitorizam o desempenho das cargas de trabalho dos contentores que são implantados para clusters kubernetes geridos ou auto-geridos. Para alertar sobre o que importa, este artigo descreve como criar alertas baseados em log para as seguintes situações com clusters AKS:

- Quando a utilização do CPU ou da memória nos nos nóns de cluster excede um limiar
- Quando a utilização da CPU ou da memória em qualquer recipiente dentro de um controlador excede um limiar em comparação com um limite fixado no recurso correspondente
- *NotReady* status node conta
- *Falhado,* *pendente*, *desconhecido,* *execução* ou *contagem de* fase de pod bem sucedida
- Quando o espaço livre do disco nos nos nos acenos de cluster excede um limiar

Para alertar para uma utilização elevada do CPU ou da memória, ou para um baixo espaço de disco livre nos nós do cluster, utilize as consultas fornecidas para criar um alerta métrico ou um alerta de medição métrica. Embora os alertas métricos tenham uma latência mais baixa do que os alertas de log, os alertas de log fornecem consulta avançada e maior sofisticação. As consultas de alerta de registo comparam uma data com a presente, utilizando o operador *agora* e recuando uma hora. (Insights de contentores armazena todas as datas no formato Tempo Universal Coordenado (UTC).)

Se não estiver familiarizado com os alertas do Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure](../alerts/alerts-overview.md) antes de começar. Para saber mais sobre os alertas que utilizam consultas de registo, consulte [alertas de registo no Azure Monitor](../alerts/alerts-unified-log.md). Para obter mais informações sobre alertas métricos, consulte [alertas métricos no Azure Monitor](../alerts/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de registo de utilização de recursos

As consultas nesta secção suportam cada cenário de alerta. São usados no passo 7 da secção de alerta de [criação](#create-an-alert-rule) deste artigo.

A seguinte consulta calcula a utilização média do CPU como uma média da utilização do CPU dos nós membros a cada minuto.  

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

A seguinte consulta calcula a utilização média da memória como uma média da utilização da memória dos nós dos membros a cada minuto.

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
>As seguintes consultas utilizam os valores de espaço reservado \<your-cluster-name> e representam o seu cluster e \<your-controller-name> controlador. Substitua-os por valores específicos do seu ambiente quando configurar alertas.

A seguinte consulta calcula a utilização média do CPU de todos os contentores num controlador como uma média de utilização do CPU de cada instância de contentor num controlador a cada minuto. A medição é uma percentagem do limite estabelecido para um recipiente.

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

A seguinte consulta calcula a utilização média da memória de todos os recipientes num controlador como uma média de utilização da memória de cada instância de recipiente num controlador a cada minuto. A medição é uma percentagem do limite estabelecido para um recipiente.

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
A seguinte consulta devolve as contagens da fase do pod com base em todas as fases: *Falhado,* *Pendente*, *Desconhecido,* *Funcionamento* ou *Bem Sucedido*.  

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
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
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
>Para alertar em determinadas fases do pod, tais como *Pendentes*, Falhados ou *Desconhecidos,* modificar a última linha da consulta.  Por exemplo, para alertar sobre a utilização *do FailedCount:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A seguinte consulta devolve discos de nós de cluster que excedem 90% de espaço livre usado. Para obter o ID do cluster, primeiro executar a seguinte consulta e copiar o valor da `ClusterId` propriedade:

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

Esta secção percorre a criação de uma regra de alerta de medição métrica utilizando dados de desempenho a partir de insights do Contentor. Você pode usar este processo básico com uma variedade de consultas de log para alertar em diferentes contadores de desempenho. Utilize uma das consultas de pesquisa de registo fornecidas anteriormente para começar. Para criar usando um modelo ARM, consulte [amostras de criação de alerta de registo usando o modelo de recurso Azure.](../alerts/alerts-log-create-templates.md)

>[!NOTE]
>O procedimento a seguir para criar uma regra de alerta para a utilização de recursos de contentores requer que você mude para um novo registo alerta API como descrito na [preferência da API da Switch para alertas de registo](../alerts/alerts-log-api-switch.md).
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal Azure, procure e selecione **espaços de trabalho do Log Analytics**.
3. Na sua lista de espaços de trabalho Log Analytics, selecione as introspeções de suporte do espaço de trabalho. 
4. No painel do lado esquerdo, selecione **Logs** para abrir a página de registos do Monitor Azure. Utilize esta página para escrever e executar consultas de registo Azure.
5. Na página **'Registares',** cole uma das [consultas fornecidas](#resource-utilization-log-search-queries) anteriormente no campo **de consulta de Pesquisa** e, em seguida, selecione **Executar** para validar os resultados. Se não realizar este passo, a opção **+Novo alerta** não está disponível para selecionar.
6. Selecione **+Novo alerta** para criar um alerta de registo.
7. Na secção **Condição,** selecione a **procura de registo personalizado sempre que a procura \<logic undefined>** de registo personalizado for pré-definida. O tipo de sinal **de pesquisa de registo personalizado** é selecionado automaticamente porque estamos a criar uma regra de alerta diretamente a partir da página de registos do Azure Monitor.  
8. Cole uma das [consultas fornecidas](#resource-utilization-log-search-queries) anteriormente no campo **de consulta de pesquisa.**
9. Configure o alerta da seguinte forma:

    1. Na lista pendente **Com base em**, selecione **Unidades métricas**. Uma medição métrica cria um alerta para cada objeto na consulta que tem um valor acima do nosso limiar especificado.
    1. Para **a Condição**, selecione Maior do **que**, e introduza **75** como limiar **de** base inicial para os alertas de utilização da memória e CPU. Para o baixo alerta de espaço em disco, introduza **90**. Ou insira um valor diferente que satisfaça os seus critérios.
    1. Na secção **Alerta de Gatilho Baseado na** secção, selecione **infrações consecutivas**. Da lista de recuos, selecione **Maior do que**, e introduza **2**.
    1. Para configurar um alerta para a utilização do CPU do recipiente ou da memória, **em agregado em**, selecione **ContainerName**. Para configurar para o alerta de disco baixo do nó do cluster, selecione **ClusterId**.
    1. Na secção **Avaliada com base na** secção, definir o valor do **período** para **60 minutos**. A regra será executada a cada 5 minutos e devolverá os registos que foram criados na última hora da hora atual. Definir o período de tempo para uma grande janela explica a latência potencial de dados. Também garante que a consulta devolve dados para evitar um falso negativo em que o alerta nunca dispara.

10. Selecione **Feito** para completar a regra de alerta.
11. Introduza um nome no campo **de nome da regra de alerta.** Especifique uma **Descrição** que forneça detalhes sobre o alerta. E selecione um nível de gravidade adequado das opções fornecidas.
12. Para ativar imediatamente a regra de alerta, aceite o valor predefinido para **Enable rule após a criação**.
13. Selecione um Grupo de **Ação** existente ou crie um novo grupo. Este passo garante que as mesmas ações são tomadas sempre que um alerta é desencadeado. Configurar com base na forma como a sua equipa de operações de TI ou DevOps gere incidentes.
14. Selecione **Criar a regra de alerta** para completar a regra de alerta. Começa imediatamente a executar.

## <a name="next-steps"></a>Passos seguintes

- Consulte [exemplos de consulta](container-insights-log-search.md#search-logs-to-analyze-data) de registos para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

- Para saber mais sobre o Azure Monitor e como monitorizar outros aspetos do seu cluster Kubernetes, consulte o desempenho do [cluster De Kubernetes](container-insights-analyze.md) e [veja a saúde do cluster Kubernetes.](./container-insights-overview.md)