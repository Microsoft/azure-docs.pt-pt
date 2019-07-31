---
title: Métricas comuns de dimensionamento automático
description: Saiba quais métricas são normalmente usadas para dimensionar automaticamente seus serviços de nuvem, máquinas virtuais e aplicativos Web.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129736"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor métricas comuns de dimensionamento automático

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor dimensionamento automático permite dimensionar o número de instâncias em execução para cima ou para baixo, com base nos dados de telemetria (métricas). Este documento descreve as métricas comuns que você pode querer usar. Na portal do Azure, você pode escolher a métrica do recurso pelo qual dimensionar. No entanto, você também pode escolher qualquer métrica de um recurso diferente para dimensionar.

Azure Monitor dimensionamento automático se aplica somente aos conjuntos de dimensionamento de [máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Outros serviços do Azure usam métodos de dimensionamento diferentes.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Computar métricas para VMs baseadas no Resource Manager
Por padrão, as máquinas virtuais baseadas no Resource Manager e os conjuntos de dimensionamento de máquinas virtuais emitem as métricas básicas (nível de host). Além disso, quando você configura a coleta de dados de diagnóstico para uma VM do Azure e VMSS, a extensão de diagnóstico do Azure também emite contadores de desempenho do SO convidado (normalmente conhecidos como "métricas do sistema operacional convidado").  Você usa todas essas métricas em regras de dimensionamento automático.

Você pode usar a `Get MetricDefinitions` API/PoSH/CLI para exibir as métricas disponíveis para o recurso VMSS.

Se você estiver usando conjuntos de dimensionamento de VM e não vir uma determinada métrica listada, ela provavelmente será desabilitada em sua extensão de diagnóstico.

Se uma determinada métrica não estiver sendo amostrada ou transferida na frequência desejada, você poderá atualizar a configuração de diagnóstico.

Se qualquer um dos casos anteriores for true, examine [usar o PowerShell para habilitar diagnóstico do Azure em uma máquina virtual que executa o Windows](../../virtual-machines/extensions/diagnostics-windows.md) sobre o PowerShell para configurar e atualizar sua extensão de diagnóstico de VM do Azure para habilitar a métrica. Esse artigo também inclui um arquivo de configuração de diagnóstico de exemplo.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas de host para VMs Windows e Linux baseadas no Resource Manager
As métricas de nível de host a seguir são emitidas por padrão para a VM do Azure e VMSS em instâncias do Windows e Linux. Essas métricas descrevem sua VM do Azure, mas são coletadas do host de VM do Azure em vez de por meio do agente instalado na VM convidada. Você pode usar essas métricas em regras de dimensionamento automático.

- [Métricas de host para VMs Windows e Linux baseadas no Resource Manager](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Métricas de host para conjuntos de dimensionamento de VM Windows e Linux baseados no Resource Manager](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Métricas do sistema operacional convidado as VMs do Windows baseadas no Resource Manager
Quando você cria uma VM no Azure, o diagnóstico é habilitado usando a extensão de diagnóstico. A extensão de diagnóstico emite um conjunto de métricas extraídas de dentro da VM. Isso significa que você pode reduzir a escalabilidade das métricas que não são emitidas por padrão.

Você pode gerar uma lista de métricas usando o comando a seguir no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode criar um alerta para as seguintes métricas:

| Nome da Métrica | Unidade |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percent |
| \Processor (_ total\% ) tempo privilegiado |Percent |
| \Processor (_ total\% ) tempo de usuário |Percent |
| \Processor Information (_ total) \Processor Frequency |Count |
| \System\Processes |Count |
| \Process (_ total) contagem de \Contagem |Count |
| \Process (_ total) contagem de \Contagem |Count |
| \Memory\% bytes confirmados em uso |Percent |
| \Memory\Available Bytes |Bytes |
| \Memory\Committed bytes |Bytes |
| Limite de \Memory\Commit |Bytes |
| \ Pagináveis bytes de paginação |Bytes |
| \ Pagináveis bytes não paginados |Bytes |
| \PhysicalDisk (_ total\% ) tempo de disco |Percent |
| \PhysicalDisk (_ total\% ) tempo de leitura do disco |Percent |
| \PhysicalDisk (_ total\% ) tempo de gravação de disco |Percent |
| \PhysicalDisk (_ total) \Bytes transferências de segundos/s |CountPerSecond |
| \PhysicalDisk (_ total) \Bytes leituras de segundos/s |CountPerSecond |
| \PhysicalDisk (_ total) gravações \Bytes/s |CountPerSecond |
| \PhysicalDisk (_ total) \Bytes de bytes/s |BytesPerSecond |
| \PhysicalDisk (_ total) \Bytes de leitura de bytes/s |BytesPerSecond |
| \PhysicalDisk (_ total) \Bytes de gravação de bytes/s |BytesPerSecond |
| \PhysicalDisk (_ total) \Avg. Comprimento da Fila de Discos |Count |
| \PhysicalDisk (_ total) \Avg. Comprimento da fila de leitura de disco |Count |
| \PhysicalDisk (_ total) \Avg. Comprimento da fila de gravação de disco |Count |
| \LogicalDisk (_ total\% ) espaço livre |Percent |
| \LogicalDisk (_ total) \Megabytes livres megabytes |Count |

### <a name="guest-os-metrics-linux-vms"></a>Métricas do SO convidado VMs Linux
Quando você cria uma VM no Azure, o diagnóstico é habilitado por padrão usando a extensão de diagnóstico.

Você pode gerar uma lista de métricas usando o comando a seguir no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Você pode criar um alerta para as seguintes métricas:

| Nome da Métrica | Unidade |
| --- | --- |
| \Memory\AvailableMemory |Bytes |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |Bytes |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Segundos |
| \PhysicalDisk\AverageWriteTime |Segundos |
| \PhysicalDisk\AverageTransferTime |Segundos |
| \PhysicalDisk\AverageDiskQueueLength |Count |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |Count |
| \NetworkInterface\PacketsReceived |Count |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \NetworkInterface\TotalCollisions |Count |

## <a name="commonly-used-web-server-farm-metrics"></a>Métricas da Web (farm de servidores) usadas com frequência
Você também pode executar o dimensionamento automático com base em métricas comuns do servidor Web, como o comprimento da fila http. O nome da métrica é **HttpQueueLength**.  A seção a seguir lista as métricas disponíveis do farm de servidores (aplicativos Web).

### <a name="web-apps-metrics"></a>Métricas de aplicativos Web
Você pode gerar uma lista de métricas de aplicativos Web usando o comando a seguir no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode alertar ou Dimensionar por essas métricas.

| Nome da Métrica | Unidade |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas de armazenamento normalmente usadas
Você pode dimensionar por comprimento da fila de armazenamento, que é o número de mensagens na fila de armazenamento. O comprimento da fila de armazenamento é uma métrica especial e o limite é o número de mensagens por instância. Por exemplo, se houver duas instâncias e o limite for definido como 100, o dimensionamento ocorrerá quando o número total de mensagens na fila for 200. Isso pode ser 100 mensagens por instância, 120 e 80, ou qualquer outra combinação que adicione até 200 ou mais.

Defina essa configuração no portal do Azure na folha **configurações** . Para conjuntos de dimensionamento de VM, você pode atualizar a configuração de dimensionamento automático no modelo do Resource Manager para usar metricname como *ApproximateMessageCount* e passar a ID da fila de armazenamento como *metricResourceUri*.

Por exemplo, com uma conta de armazenamento clássica, a configuração de dimensionamento automático Metrictrigger incluiria incluiria:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Para uma conta de armazenamento (não clássico), o Metrictrigger incluiria incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas do barramento de serviço comumente usadas
Você pode dimensionar por comprimento da fila do barramento de serviço, que é o número de mensagens na fila do barramento de serviço. O comprimento da fila do barramento de serviço é uma métrica especial e o limite é o número de mensagens por instância. Por exemplo, se houver duas instâncias e o limite for definido como 100, o dimensionamento ocorrerá quando o número total de mensagens na fila for 200. Isso pode ser 100 mensagens por instância, 120 e 80, ou qualquer outra combinação que adicione até 200 ou mais.

Para conjuntos de dimensionamento de VM, você pode atualizar a configuração de dimensionamento automático no modelo do Resource Manager para usar metricname como *ApproximateMessageCount* e passar a ID da fila de armazenamento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para o barramento de serviço, o conceito de grupo de recursos não existe, mas Azure Resource Manager cria um grupo de recursos padrão por região. O grupo de recursos geralmente está no formato ' default-ServiceBus-[Region] '. Por exemplo, "default-ServiceBus-Lesteus", "default-ServiceBus-Oesteus", "default-ServiceBus-AustraliaEast" etc.
>
>

