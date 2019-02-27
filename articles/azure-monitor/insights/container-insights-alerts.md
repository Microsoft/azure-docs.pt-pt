---
title: Criar alertas de desempenho com o Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve como pode criar alertas do Azure personalizadas com base em consultas de registo de memória e utilização da CPU do Azure Monitor para contentores.
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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886783"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como posso configurar alertas para problemas de desempenho no Azure Monitor para contentores
O Azure Monitor para monitores de contentores o desempenho de cargas de trabalho do contentor implementada em qualquer uma das instâncias de contentor do Azure ou geridos clusters do Kubernetes alojados no Azure Kubernetes Service (AKS). 

Este artigo descreve como ativar os alertas nas seguintes situações:

* Quando utilização de CPU e memória em nós do cluster ou excede o limite definido.
* Quando a utilização de CPU ou memória em qualquer um dos contentores dentro de um controlador excede o limiar definido em comparação com o limite definido no recurso correspondente.

Para o alertar quando a utilização de CPU ou memória é alta para um cluster ou um controlador, crie uma regra de alerta de medida da métrica baseada em consultas de registo fornecidas. As consultas comparar um datetime ao presente usando o operador de agora e retorna uma hora. Todas as datas armazenadas pelo Monitor do Azure para contentores são em formato UTC.

Antes de iniciar, se não estiver familiarizado com alertas no Azure Monitor, consulte [descrição geral dos alertas no Microsoft Azure](../platform/alerts-overview.md). Para obter mais informações sobre alertas através de consultas de registo, consulte [alertas de registo no Azure Monitor](../platform/alerts-unified-log.md)

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de registo de utilização de recursos
As consultas nesta secção são fornecidas para oferecer suporte a cada cenário de alerta. As consultas são necessárias para o passo 7 sob o [criar alerta](#create-alert-rule) secção abaixo.  

A seguinte consulta calcula a utilização média da CPU, como uma média de utilização de CPU de nós do membro a cada minuto.  

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
>Consultas abaixo contêm valores de cadeia de caracteres de marcador de posição para os nomes de cluster e o controlador - < nome da-cluster-> e < your-controlador-name >. Substitua os marcadores de posição pelos valores específicos ao seu ambiente antes de configurar alertas. 


A seguinte consulta calcula a utilização de CPU média de todos os contentores num controlador como uma média de utilização da CPU de cada instância de contentor num controlador de todos os minutos como uma percentagem do limite definido para um contentor.

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

A seguinte consulta calcula a utilização da memória média, de todos os contentores num controlador, como uma média de utilização da memória de cada instância de contentor num controlador de todos os minutos como uma percentagem do limite definido para um contentor.

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

## <a name="create-alert-rule"></a>Criar regra de alerta
Execute os seguintes passos para criar um alerta de registo no Azure Monitor com as regras de pesquisa de registo fornecido anteriormente.  

>[!NOTE]
>O procedimento abaixo tem de mudar para a nova API de alertas do Log, conforme descrito em [preferência de API de mudar para os alertas de registo](../platform/alerts-log-api-switch.md) se estiver a criar uma regra de alerta para a utilização de recursos de contentor. 
>

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Monitor** do painel esquerdo no portal do Azure. Sob o **Insights** secção, selecione **contentores**.    
3. Partir do **monitorizados Clusters** separador, selecione um cluster a partir da lista ao clicar no nome do cluster.
4. No painel esquerdo, no **monitorização** secção, selecione **registos** para abrir o Azure Monitor registos de página, que é utilizado para criar e executar consultas do Log Analytics do Azure.
5. Sobre o **Logs** página, clique em **+ nova regra de alerta**.
6. Sob o **condição** secção, clique na condição de registo personalizado predefinido **sempre que o registo pesquisa personalizada é <logic undefined>** . O **pesquisa de registos personalizado** tipo de sinal é selecionado automaticamente para nós porque foi iniciada a criação de uma regra de alerta diretamente a partir da página de registos do Azure Monitor.  
7. Cole um da [consultas](#resource-utilization-log-search-queries) fornecido anteriormente para o **consulta de pesquisa** campo. 

8. Configure o alerta com as seguintes informações:

    a. Na lista pendente **Com base em**, selecione **Unidades métricas**. Uma medição métrica cria um alerta para cada objeto na consulta com um valor que excede o nosso limiar especificado.  
    b. Para o **condição**, selecione **superior** e introduza **75** como uma linha de base inicial **limiar** ou introduza um valor que cumpra sua critérios.  
    c. Sob **acionar alerta com base no** secção, selecione **falhas consecutivas** e para a lista pendente, selecione **superior** introduza um valor de **2**.  
    d. Se configurar um alerta para o contentor da CPU ou de utilização da memória, em **agregada no** selecionar **ContainerName** na lista pendente.  
    e. Sob **Evaluated com base nos** secção, modifique o **período** valor a 60 minutos. A regra será executado a cada cinco minutos e devolver os registos que foram criados na última hora da hora atual. Definir o período de tempo para uma janela maior tem em conta o potencial da latência de dados e garante que a consulta devolve dados para evitar um falso negativo em que o alerta nunca é acionado. 

9. Clique em **Concluído** para concluir a regra de alerta.
10. Forneça um nome de seu alerta no **nome da regra de alerta** campo. Especifique um **Descrição** com detalhes sobre as especificações para o alerta e selecione uma gravidade de apropriado entre as opções fornecidas.
11. Para ativar imediatamente a regra de alerta na criação, aceite o valor predefinido de **Ativar regra após criação**.
12. Para o passo final, selecione um existente ou crie um novo **grupo de ação**, que garante que as mesmas ações direcionadas sempre que um alerta é acionado e pode ser utilizado para cada regra que definir. Configurar com base em como o departamento de TI ou gere as operações de DevOps incidentes. 
13. Clique em **Criar regra de alerta** para concluir a regra de alerta. Começa imediatamente a executar.

## <a name="next-steps"></a>Passos Seguintes

* Rever alguns da [exemplos de consulta de registo](container-insights-analyze.md#search-logs-to-analyze-data) Saiba mais sobre as consultas predefinidas ou exemplos para avaliar e utilizar ou personalizar para outros cenários de alerta. 
* Para continuar a aprender como utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, consulte [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md)
