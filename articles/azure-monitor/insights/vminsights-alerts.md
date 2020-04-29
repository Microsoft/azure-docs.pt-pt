---
title: Alertas do Monitor Azure para VMs
description: Descreve como criar regras de alerta a partir de dados de desempenho recolhidos pelo Azure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289605"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Como criar alertas do Monitor Azure para VMs
[Os alertas no Monitor Azure](../platform/alerts-overview.md) notificam-no proativamente de dados e padrões interessantes nos seus dados de monitorização. O Monitor Azure para VMs não inclui regras de alerta pré-configuradas, mas pode criar o seu próprio com base em dados que recolhe. Este artigo fornece orientações sobre a criação de regras de alerta, incluindo um conjunto de consultas de amostra.


## <a name="alert-rule-types"></a>Tipos de regra de alerta
O Azure Monitor tem [diferentes tipos de regras de alerta](../platform/alerts-overview.md#what-you-can-alert-on) com base nos dados utilizados para criar o alerta. Todos os dados recolhidos pelo Azure Monitor para VMs são armazenados em Registos do Monitor Azure que [suportaalertas de registo](../platform/alerts-log.md). Não é possível utilizar atualmente [alertas métricos](../platform/alerts-log.md) com dados de desempenho recolhidos do Monitor Azure para VMs porque os dados não são recolhidos nas Métricas do Monitor Do Azure. Para recolher dados para alertas métricos, instale a [extensão](../platform/diagnostics-extension-overview.md) de diagnóstico para VMs do Windows ou o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) para vMs Linux para recolher dados de desempenho em Métricas.

Existem dois tipos de alertas de registo no Monitor Azure:

- [O número de alertas de resultados](../platform/alerts-unified-log.md#number-of-results-alert-rules) cria um único alerta quando uma consulta devolve pelo menos um número especificado de registos. Estes são ideais para dados não numéricos tais e eventos Windows e Syslog recolhidos pelo [agente Log Analytics](../platform/log-analytics-agent.md) ou para analisar tendências de desempenho em vários computadores.
- [Os alertas](../platform/alerts-unified-log.md#metric-measurement-alert-rules) de medição métrica criam um alerta separado para cada registo numa consulta que tem um valor superior a um limiar definido na regra de alerta. Estas regras de alerta são ideais para dados de desempenho recolhidos pelo Azure Monitor para VMs, uma vez que podem criar alertas individuais para cada computador.


## <a name="alert-rule-walkthrough"></a>Regra de alerta walkthrough
Esta secção atravessa a criação de uma regra de alerta de medição métrica utilizando dados de desempenho do Monitor Azure para VMs. Pode utilizar este processo básico com uma variedade de consultas de registo para alertar em diferentes contadores de desempenho.

Comece por criar uma nova regra de alerta seguindo o procedimento em [Criar, visualizar e gerir alertas](../platform/alerts-log.md)de registo utilizando o Monitor Azure . Para o **Recurso,** selecione o espaço de trabalho log Analytics que os VMs do Monitor Azure usam na sua subscrição. Uma vez que o recurso-alvo para regras de alerta de registo é sempre um espaço de trabalho de Log Analytics, a consulta de registo deve incluir qualquer filtro para máquinas virtuais específicas ou conjuntos de escala de máquinas virtuais. 

Para o **estado** da regra do alerta, utilize uma das consultas na [secção abaixo](#sample-alert-queries) como consulta de **pesquisa**. A consulta deve devolver uma propriedade numérica chamada *AggregatedValue*. Deve resumir os dados por computador para que possa criar um alerta separado para cada máquina virtual que exceda o limiar.

Na **lógica de alerta,** selecione **a medição métrica** e, em seguida, forneça um **valor limite**. No **alerta de gatilho baseado em ,** especifique quantas vezes o limiar deve ser ultrapassado antes de ser criado um alerta. Por exemplo, provavelmente não se importa se o processador excede um limiar uma vez e depois volta ao normal, mas importa-se se continua a exceder o limiar ao longo de várias medições consecutivas.

O **Avaliado com base na** secção define a frequência com que a consulta é executada e a janela de tempo para a consulta. No exemplo abaixo apresentado, a consulta será executada a cada 15 minutos e avaliará os valores de desempenho recolhidos ao longo dos 15 minutos anteriores.


![Regra de alerta de medição métrica](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Consultas de alerta de amostra
As seguintes consultas podem ser utilizadas com uma regra de alerta de medição métrica utilizando dados de desempenho recolhidos pelo Monitor Azure para VMs. Cada um resume os dados por computador para que seja criado um alerta para cada computador com um valor superior ao limiar.

### <a name="cpu-utilization"></a>Utilização da CPU

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Memória Disponível em MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Memória Disponível em percentagem

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Disco lógico utilizado - todos os discos em cada computador

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Disco lógico utilizado - discos individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS de disco lógico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Taxa de dados lógicas do disco

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Bytes de interfaces de rede recebidos - todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Bytes de interfaces de rede recebidos - interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Bytes de interfaces de rede enviados - todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Bytes de interfaces de rede enviados - interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Conjuntos de dimensionamento de máquinas virtuais
Modifique com o seu ID de subscrição, grupo de recursos e nome de conjunto de escala de máquina virtual.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Máquina virtual específica
Modifique com o seu ID de subscrição, grupo de recursos e nome VM.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Utilização do CPU para todos os recursos do cálculo numa subscrição
Modifique com o seu ID de subscrição.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Utilização do CPU para todos os recursos da computação num grupo de recursos
Modifique com o seu ID de subscrição e grupo de recursos.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [alertas no Monitor Azure.](../platform/alerts-overview.md)
- Saiba mais sobre consultas de [log utilizando dados do Monitor Azure para VMs](vminsights-log-search.md).
