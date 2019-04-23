---
title: Criar alertas de desempenho com o Monitor do Azure para contentores | Documentos da Microsoft
description: Este artigo descreve como utilizar o Azure Monitor para contentores para criar alertas personalizados com base em consultas de registo de memória e utilização da CPU.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791546"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como posso configurar alertas para problemas de desempenho no Azure Monitor para contentores
Monitor do Azure para contentores monitora o desempenho de cargas de trabalho de contentor que são implementadas no Azure Container Instances, ou para Kubernetes clusters geridos que estão alojados no Azure Kubernetes Service (AKS).

Este artigo descreve como ativar alertas nas seguintes situações:

* Quando a utilização da CPU ou memória em nós do cluster excede um limiar definido
* Quando a utilização da CPU ou memória em qualquer contentor dentro de um controlador excede um limiar definido em comparação com um limite que está definido no recurso correspondente
* *NotReady* contagens de nó de estado
*  *Falha*, *pendente*, *desconhecido*, *executar*, ou *bem-sucedido* contagens de pod fase

Para o alertar para elevada da CPU ou a utilização da memória em nós de cluster, utilize as consultas que são fornecidas para criar um alerta de métrica ou um alerta de medida da métrica. Alertas de métricas têm uma latência inferior à alertas de registo. Mas os alertas de registo fornecem consultas avançadas e sofisticação maior. Consultas de comparam um datetime ao presente através de alertas de registo do *agora* operador e vai fazer uma cópia de uma hora. (O Monitor do azure para contentores armazena todas as datas no formato de hora Universal Coordenada (UTC).)

Se não estiver familiarizado com alertas do Azure Monitor, consulte [descrição geral dos alertas no Microsoft Azure](../platform/alerts-overview.md) antes de começar. Para saber mais sobre os alertas que utilizam consultas de registo, veja [alertas de registo no Azure Monitor](../platform/alerts-unified-log.md). Para mais informações sobre alertas de métricas, veja [alertas de métricas no Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de registo de utilização de recursos
As consultas nesta secção suportam cada cenário de alerta. Elas são usadas no passo 7 da [criar alerta](#create-an-alert-rule) seção deste artigo.

A seguinte consulta calcula a média de utilização da CPU como uma média de utilização da CPU de nós de membros a cada minuto.  

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

A seguinte consulta calcula a utilização média da memória como uma média de utilização de memória de nós do membro a cada minuto.

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
>As seguintes consultas de utilizam os valores de marcador de posição \<nome da cluster > e \<nome da controlador > para representar o cluster e o controlador. Substituí-las com valores específicos ao seu ambiente ao configurar alertas.

A seguinte consulta calcula a utilização de CPU média de todos os contentores num controlador como uma média de utilização da CPU de cada instância de contentor num controlador de cada minuto. A medição é uma percentagem do limite definido para um contentor.

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

A seguinte consulta calcula a utilização de memória média de todos os contentores num controlador, como uma média de utilização da memória de cada instância de contentor num controlador de cada minuto. A medição é uma percentagem do limite definido para um contentor.

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

A seguinte consulta devolve todos os nós e contagens de que têm o estado *pronto* e *NotReady*.

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
A seguinte consulta volta contagens de fase de pod com base em todas as fases: *Falha*, *pendente*, *desconhecido*, *executar*, ou *foi concluída com êxito*.  

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
>Para o alertar sobre determinadas fases de pod, tal como *pendente*, *com falhas*, ou *desconhecido*, modificar a última linha da consulta. Por exemplo, para alertar relativamente *FailedCount* utilizar: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Siga estes passos para criar um alerta de registo no Azure Monitor, utilizando as regras de pesquisa de registo que foi fornecida anteriormente.  

>[!NOTE]
>O procedimento seguinte para criar uma regra de alerta para a utilização de recursos do contentor tem de mudar para um novo registo de API de alertas, conforme descrito em [preferência de API de mudar para os alertas de registo](../platform/alerts-log-api-switch.md).
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** partir do painel no lado esquerdo. Sob **Insights**, selecione **contentores**.
3. Sobre o **monitorizados Clusters** separador, selecione um cluster a partir da lista.
4. No painel à esquerda sob **monitorização**, selecione **registos** para abrir a página de registos do Azure Monitor. Utilize esta página para criar e executar consultas do Log Analytics do Azure.
5. Sobre o **Logs** página, selecione **+ nova regra de alerta**.
6. Na **condição** secção, selecione a **sempre que o registo pesquisa personalizada é \<lógica não definida >** predefinidas condição de registo personalizado. O **pesquisa de registos personalizado** tipo de sinal é selecionado automaticamente uma vez que estamos a criar uma regra de alerta diretamente a partir da página de registos do Azure Monitor.  
7. Cole um da [consultas](#resource-utilization-log-search-queries) fornecido anteriormente para o **consulta de pesquisa** campo.
8. Configure o alerta da seguinte forma:

    1. Na lista pendente **Com base em**, selecione **Unidades métricas**. Uma medida da métrica cria um alerta para cada objeto na consulta que contém um valor superior ao nosso limiar especificado.
    1. Para **condição**, selecione **superior**e introduza **75** como uma linha de base inicial **limiar**. Ou introduza um valor diferente que atende a seus critérios.
    1. Na **acionar alerta com base no** secção, selecione **falhas consecutivas**. Na lista pendente, selecione **maior**e introduza **2**.
    1. Para configurar um alerta para o contentor da CPU ou de utilização da memória, em **agregada no**, selecione **ContainerName**. 
    1. No **Evaluated com base no** secção, defina o **período** valor **60 minutos**. A regra será executado a cada 5 minutos e devolver os registos que foram criados na última hora da hora atual. Definir o período de tempo como contas uma janela ampla potencial latência de dados. Também garante que a consulta retorna dados para evitar falso negativo em que o alerta nunca é acionado.

9. Selecione **feito** para concluir a regra de alerta.
10. Introduza um nome na **nome da regra de alerta** campo. Especifique um **Descrição** que fornece detalhes sobre o alerta. E selecione um nível de gravidade apropriado entre as opções fornecidas.
11. Para ativar imediatamente a regra de alerta, aceite o valor predefinido para **ativar regra após a criação**.
12. Selecione um existente **grupo de ação** ou criar um novo grupo. Este passo garante que as mesmas ações direcionadas sempre que um alerta é acionado. Configurar com base em como o departamento de TI ou a equipe de operações de DevOps gerencia a incidentes.
13. Selecione **criar regra de alerta** para concluir a regra de alerta. Começa imediatamente a executar.

## <a name="next-steps"></a>Passos Seguintes

* Modo de exibição [exemplos de consulta de registo](container-insights-analyze.md#search-logs-to-analyze-data) para saber mais sobre consultas predefinidas e exemplos para avaliar ou personalizar para outros cenários de alerta.
* Para saber mais sobre o Azure Monitor e como monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
