---
title: Métricas comuns do dimensionamento automático
description: Saiba as métricas são frequentemente utilizadas para o dimensionamento automático seus serviços Cloud, máquinas virtuais e aplicações Web.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312057"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comuns do Azure Monitor dimensionamento automático

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dimensionamento automático de Monitor do Azure permite-lhe dimensionar o número de instâncias em execução ou reduzir verticalmente, com base nos dados de telemetria (Métrica). Este documento descreve as métricas comuns que pode querer utilizar. No portal do Azure, pode escolher a métrica do recurso para reduzir horizontalmente para. No entanto, também pode escolher qualquer métrica entre um recurso diferente para reduzir horizontalmente para.

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Outros serviços do Azure utilizam diferentes métodos de dimensionamento.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Métricas de computação para VMs baseadas no Resource Manager
Por predefinição, as máquinas virtuais baseadas no Resource Manager e conjuntos de dimensionamento de máquinas virtuais emitem métricas básicas de (ao nível do anfitrião). Além disso, quando configurar a recolha de dados de diagnóstico para uma VM do Azure e o VMSS, a extensão de diagnóstico do Azure emite também contadores de desempenho de SO convidado (conhecidos como "Métricas de SO convidado").  Utilize todas estas métricas de regras de dimensionamento automático.

Pode utilizar o `Get MetricDefinitions` PoSH/API/CLI para ver as métricas disponíveis para o seu recurso VMSS.

Se estiver a utilizar conjuntos de dimensionamento VM e não vir uma métrica em particular listada, então é provável *desativada* na sua extensão de diagnóstico.

Se uma métrica em particular não está a ser objeto de amostragem ou transferidos com a frequência que quiser, pode atualizar a configuração de diagnósticos.

Se ambos os casos anterior for VERDADEIRO, em seguida, reveja [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual a executar o Windows](../../virtual-machines/extensions/diagnostics-windows.md) sobre o PowerShell para configurar e atualizar a extensão de diagnóstico de VM do Azure para ativar a métrica. Esse artigo também inclui um ficheiro de configuração de diagnósticos de exemplo.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas de anfitrião para o Windows baseados no Resource Manager e VMs do Linux
As seguintes métricas ao nível do anfitrião são emitidas por padrão para VM do Azure e o VMSS em instâncias do Windows e Linux. Estas métricas descrevem a sua VM do Azure, mas são recolhidas a partir do anfitrião de VM do Azure, em vez de através do agente instalado na VM do convidado. Pode utilizar estas métricas nas regras de dimensionamento automático.

- [Métricas de anfitrião para o Windows baseados no Resource Manager e VMs do Linux](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Métricas de anfitrião para o Windows baseados no Resource Manager e os conjuntos de dimensionamento de VM do Linux](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Métricas de SO convidado baseado no Resource Manager VMs do Windows
Quando cria uma VM no Azure, o diagnóstico está ativado ao utilizar a extensão de diagnóstico. A extensão de diagnóstico emite um conjunto de métricas obtidas a partir de dentro da VM. Isso significa que pode o dimensionamento automático do métricas que não são emitidos por predefinição.

Pode gerar uma lista das métricas com o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode criar um alerta para as métricas seguintes:

| Nome da Métrica | Unidade |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentagem |
| \Processor(_Total)\% Privileged Time |Percentagem |
| \Processor(_Total)\% de tempo do utilizador |Percentagem |
| \Processor Information(_Total)\Processor Frequency |Contagem |
| \System\Processes |Contagem |
| Contagem de \Thread \Process ( total) |Contagem |
| Contagem de \Handle \Process ( total) |Contagem |
| \Memory\% Bytes dedicados em utilização |Percentagem |
| \Memory\Available Bytes |Bytes |
| Bytes de \Memory\Committed |Bytes |
| Limite de \Memory\Commit |Bytes |
| Bytes de \Memory\Pool bloco paginado |Bytes |
| \Memory\Pool memória não paginável |Bytes |
| \PhysicalDisk(_Total)\% Disk Time |Percentagem |
| \PhysicalDisk(_Total)\% Disk Read Time |Percentagem |
| \PhysicalDisk(_Total)\% hora de gravação em disco |Percentagem |
| \PhysicalDisk ( total) \Disk transferências/seg. |CountPerSecond |
| \PhysicalDisk(_Total)\Disk Reads/sec |CountPerSecond |
| \PhysicalDisk(_Total)\Disk Writes/sec |CountPerSecond |
| \PhysicalDisk ( total) \Disk bytes/seg. |BytesPerSecond |
| \Disk \PhysicalDisk ( total) Bytes lidos/seg |BytesPerSecond |
| Escrita de \Disk \PhysicalDisk ( total) Bytes/seg |BytesPerSecond |
| \Avg \PhysicalDisk ( total). Comprimento da Fila de Discos |Contagem |
| \Avg \PhysicalDisk ( total). Comprimento de fila de leitura do disco |Contagem |
| \Avg \PhysicalDisk ( total). Comprimento de fila de escrita de disco |Contagem |
| \LogicalDisk(_Total)\% espaço livre |Percentagem |
| Megabytes de \Free \LogicalDisk ( total) |Contagem |

### <a name="guest-os-metrics-linux-vms"></a>Métricas de SO convidado VMs do Linux
Quando cria uma VM no Azure, o diagnóstico está ativado por predefinição, utilizando a extensão de diagnóstico.

Pode gerar uma lista das métricas com o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Pode criar um alerta para as métricas seguintes:

| Nome da Métrica | Unidade |
| --- | --- |
| \Memory\AvailableMemory |Bytes |
| \Memory\PercentAvailableMemory |Percentagem |
| \Memory\UsedMemory |Bytes |
| \Memory\PercentUsedMemory |Percentagem |
| \Memory\PercentUsedByCache |Percentagem |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percentagem |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percentagem |
| \Processor\PercentIdleTime |Percentagem |
| \Processor\PercentUserTime |Percentagem |
| \Processor\PercentNiceTime |Percentagem |
| \Processor\PercentPrivilegedTime |Percentagem |
| \Processor\PercentInterruptTime |Percentagem |
| \Processor\PercentDPCTime |Percentagem |
| \Processor\PercentProcessorTime |Percentagem |
| \Processor\PercentIOWaitTime |Percentagem |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Segundos |
| \PhysicalDisk\AverageWriteTime |Segundos |
| \PhysicalDisk\AverageTransferTime |Segundos |
| \PhysicalDisk\AverageDiskQueueLength |Contagem |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |Contagem |
| \NetworkInterface\PacketsReceived |Contagem |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Contagem |
| \NetworkInterface\TotalTxErrors |Contagem |
| \NetworkInterface\TotalCollisions |Contagem |

## <a name="commonly-used-web-server-farm-metrics"></a>Métricas frequentemente utilizadas da Web (Serverová Farma)
Também pode efetuar o dimensionamento automático com base nas métricas de servidor web comuns, como o comprimento da fila de Http. Nome métrico está **HttpQueueLength**.  A secção seguinte apresenta uma lista de métricas de farm (aplicações Web) do servidor disponíveis.

### <a name="web-apps-metrics"></a>Métricas de aplicações Web
Pode gerar uma lista das métricas de aplicações Web com o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode alertar ou dimensionar por estas métricas.

| Nome da Métrica | Unidade |
| --- | --- |
| CpuPercentage |Percentagem |
| MemoryPercentage |Percentagem |
| DiskQueueLength |Contagem |
| HttpQueueLength |Contagem |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas de armazenamento frequentemente utilizadas
Pode aumentar ao comprimento da fila de armazenamento, que é o número de mensagens na fila de armazenamento. Comprimento da fila de armazenamento é uma métrica especial e o limite é o número de mensagens por instância. Por exemplo, se existirem duas instâncias, e se o limiar é definido para 100, dimensionamento ocorre quando o número total de mensagens na fila é 200. Isso pode ser 100 mensagens por instância, 120 e 80 ou qualquer combinação que adiciona até 200 ou mais.

Configurar esta definição no portal do Azure no **definições** painel. Para conjuntos de dimensionamento VM, pode atualizar a definição de dimensionamento automático no modelo do Resource Manager para utilizar *metricName* como *ApproximateMessageCount* e transmitir o identificador da fila de armazenamento como  *metricResourceUri*.

Por exemplo, com uma conta de armazenamento clássica metricTrigger de definição de dimensionamento automático incluem:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Para uma conta de armazenamento (não clássica), o metricTrigger incluem:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas frequentemente utilizadas do Service Bus
Pode aumentar ao comprimento da fila do Service Bus, que é o número de mensagens em fila do Service Bus. Comprimento da fila do Service Bus é uma métrica especial e o limite é o número de mensagens por instância. Por exemplo, se existirem duas instâncias, e se o limiar é definido para 100, dimensionamento ocorre quando o número total de mensagens na fila é 200. Isso pode ser 100 mensagens por instância, 120 e 80 ou qualquer combinação que adiciona até 200 ou mais.

Para conjuntos de dimensionamento VM, pode atualizar a definição de dimensionamento automático no modelo do Resource Manager para utilizar *metricName* como *ApproximateMessageCount* e transmitir o identificador da fila de armazenamento como  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para o Service Bus, o conceito de grupo de recursos não existe mas do Azure Resource Manager cria um grupo de recursos padrão por região. O grupo de recursos é normalmente no formato 'Default - ServiceBus-[Região]'. Por exemplo, "Predefinição-ServiceBus-EastUS", 'Predefinição-ServiceBus-WestUS', 'predefinido-ServiceBus-AustraliaEast"etc.
>
>

