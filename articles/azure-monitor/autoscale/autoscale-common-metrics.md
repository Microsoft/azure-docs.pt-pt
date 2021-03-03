---
title: Métricas comuns de autoescala
description: Saiba quais métricas são comumente usadas para automatizar os seus Serviços cloud, máquinas virtuais e aplicações web.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 4b763f39d3b88a7884e89dddbc2c483c1bb84d31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717779"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor autoscaling métricas comuns

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A autoscalagem do Azure Monitor permite escalar o número de casos em execução para cima ou para baixo, com base em dados de telemetria (métricas). Este documento descreve métricas comuns que pode querer usar. No portal Azure, pode escolher a métrica do recurso à escala. No entanto, também pode escolher qualquer métrica de um recurso diferente para escala por.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md) Outros serviços Azure utilizam diferentes métodos de escala.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Métricas de cálculo para VMs baseados em gestores de recursos
Por predefinição, máquinas virtuais baseadas em recursos e conjuntos de balança de máquinas virtuais emitem métricas básicas (nível de hospedeiro). Além disso, ao configurar a recolha de dados de diagnóstico para um Azure VM e VMSS, a extensão de diagnóstico Azure também emite contadores de desempenho guest-OS (vulgarmente conhecidos como "métricas de guest-OS").  Usas todas estas métricas em regras de autoescala.

Pode utilizar a `Get MetricDefinitions` API/PoSH/CLI para visualizar as métricas disponíveis para o seu recurso VMSS.

Se estiver a usar conjuntos de escala VM e não vir uma determinada lista métrica, então é provável que seja *desativada* na extensão de diagnóstico.

Se uma determinada métrica não estiver a ser amostrada ou transferida na frequência desejada, pode atualizar a configuração do diagnóstico.

Se um dos casos anteriores for verdadeiro, em seguida, [reveja Use PowerShell para permitir que o Azure Diagnostics numa máquina virtual que executa o Windows](../../virtual-machines/extensions/diagnostics-windows.md) sobre o PowerShell para configurar e atualizar a extensão de Diagnóstico Azure VM para ativar a métrica. Este artigo também inclui um ficheiro de configuração de diagnóstico de amostra.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas de anfitrião para Windows e Linux VMs baseados em recursos
As seguintes métricas de nível de hospedeiro são emitidas por padrão para Azure VM e VMSS em ambos os casos Windows e Linux. Estas métricas descrevem o seu Azure VM, mas são recolhidas do anfitrião Azure VM em vez de através de um agente instalado no VM convidado. Pode utilizar estas métricas em regras de autoescalagem.

- [Métricas de anfitrião para Windows e Linux VMs baseados em recursos](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)
- [Métricas de anfitrião para conjuntos de escalas de Windows e Linux VM baseados em recursos](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Métricas de SO de hóspedes para VMs do Windows baseados em gestores de recursos
Quando cria um VM em Azure, o diagnóstico é ativado utilizando a extensão de Diagnóstico. A extensão de diagnóstico emite um conjunto de métricas retiradas do interior do VM. Isto significa que pode autoescalificar-se de métricas que não são emitidas por padrão.

Pode gerar uma lista das métricas utilizando o seguinte comando em PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode criar um alerta para as seguintes métricas:

| Nome da Métrica | Unidade |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentagem |
| \Tempo privilegiado do processador (_Total) \% |Percentagem |
| \Tempo de utilizador do processador (_Total) \% |Percentagem |
| \Informação do processador (_Total)\Frequência do processador |de palavras |
| \Sistema\Processos |de palavras |
| \Processo (_Total)\Contagem de fios |de palavras |
| \Processo (_Total)\Contagem de manuseação |de palavras |
| \%\Bytes comprometidos com a memória em uso |Percentagem |
| \Memory\Available Bytes |Bytes |
| \Memory\Bytes comprometidos |Bytes |
| \Limite de compromisso de memória\ |Bytes |
| \Memory\Pool Paged Bytes |Bytes |
| \Memory\Pool Bytes não paged |Bytes |
| \PhysicalDisk (_Total) \% Tempo do disco |Percentagem |
| \PhysicalDisk (_Total) \% Tempo de leitura do disco |Percentagem |
| \PhysicalDisk (_Total) \% Tempo de escrita do disco |Percentagem |
| \PhysicalDisk (_Total)\Transferências de disco/seg |CondePerSecond |
| \PhysicalDisk (_Total)\Leituras de disco/seg |CondePerSecond |
| \PhysicalDisk (_Total)\Discos de disco/seg |CondePerSecond |
| \PhysicalDisk (_Total)\Disk Bytes/sec |BytesPerSecond |
| \PhysicalDisk (_Total)\Disk Read Bytes/sec |BytesPerSecond |
| \PhysicalDisk (_Total)\Disk Write Bytes/sec |BytesPerSecond |
| \PhysicalDisk (_Total)\Avg. Comprimento da fila do disco |de palavras |
| \PhysicalDisk (_Total)\Avg. Disco leia o comprimento da fila |de palavras |
| \PhysicalDisk (_Total)\Avg. Disco escrever comprimento da fila |de palavras |
| \LogicalDisk (_Total) \% Espaço Livre |Percentagem |
| \LogicalDisk(_Total)\Megabytes grátis |de palavras |

### <a name="guest-os-metrics-linux-vms"></a>Métricas de SO convidados Linux VMs
Quando cria um VM em Azure, o diagnóstico é ativado por padrão utilizando a extensão de Diagnóstico.

Pode gerar uma lista das métricas utilizando o seguinte comando em PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Pode criar um alerta para as seguintes métricas:

| Nome da Métrica | Unidade |
| --- | --- |
| \Memória\AvailableMemory |Bytes |
| \Memória\por cento DisponívelMemor |Percentagem |
| \Memória\UsedMemory |Bytes |
| \Memória\PercentUsedMemory |Percentagem |
| \Memória\PercentUsedByCache |Percentagem |
| \Memória\PagesPerSec |CondePerSecond |
| \Memória\PagesReadPerSec |CondePerSecond |
| \Memória\PagesWrittenPerSec |CondePerSecond |
| \Memória\DisponívelSwap |Bytes |
| \Memória\Por cento DisponívelSwap |Percentagem |
| \Memória\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percentagem |
| \Processador\PercentIdleTime |Percentagem |
| \Processador\PercentUserTime |Percentagem |
| \Processador\%%Time |Percentagem |
| \Processador\%PrivilegedTime |Percentagem |
| \Processador\%InterruptTime |Percentagem |
| \Processador\%3PCTime |Percentagem |
| \Processador\% Processortime |Percentagem |
| \Processador\%IOWaitTime |Percentagem |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CondePerSecond |
| \PhysicalDisk\ReadsPerSecond |CondePerSecond |
| \PhysicalDisk\WritesPerSecond |CondePerSecond |
| \PhysicalDisk\AverageReadTime |Segundos |
| \PhysicalDisk\AverageWriteTime |Segundos |
| \PhysicalDisk\AverageTransferTime |Segundos |
| \PhysicalDisk\AverageDiskQueueLength |de palavras |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |de palavras |
| \NetworkInterface\PacketsReceived |de palavras |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |de palavras |
| \NetworkInterface\TotalTxErrors |de palavras |
| \NetworkInterface\TotalCollisions |de palavras |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Métricas do Serviço de Aplicações (Server Farm) comumente utilizadas
Também pode realizar uma escala automática com base em métricas comuns do servidor web, como o comprimento da fila http. O seu nome métrico é **HttpQueueLength.**  A secção seguinte lista as métricas disponíveis da fazenda de servidores (App Service).

### <a name="web-apps-metrics"></a>Métricas de Aplicações Web
Pode gerar uma lista das métricas de Aplicações Web utilizando o seguinte comando em PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode alertar ou escalar por estas métricas.

| Nome da Métrica | Unidade |
| --- | --- |
| CpuPercentage |Percentagem |
| MemóriaPercentage |Percentagem |
| DiskQueueLength |de palavras |
| HttpQueueLength |de palavras |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas de armazenamento comumente utilizadas
Pode escalar pelo comprimento da fila de armazenamento, que é o número de mensagens na fila de armazenamento. O comprimento da fila de armazenamento é uma métrica especial e o limiar é o número de mensagens por exemplo. Por exemplo, se houver duas instâncias e se o limiar for fixado para 100, o escalonamento ocorre quando o número total de mensagens na fila é de 200. Podem ser 100 mensagens por exemplo, 120 e 80, ou qualquer outra combinação que acrescente até 200 ou mais.

Configure esta definição no portal Azure na lâmina **Definições.** Para conjuntos de escala VM, pode atualizar a definição de Autoescala no modelo de Gestor de Recursos para utilizar o *nome métrico* como *ApproximateMessageCount* e passar o ID da fila de armazenamento como *metricResourceUri*.

Por exemplo, com uma Conta de Armazenamento Clássico, a definição automática de métricostrigger incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Para uma conta de armazenamento (não clássica), o metricTrigger incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas de autocarro de serviço comumente utilizadas
Pode escalar pelo comprimento da fila do Service Bus, que é o número de mensagens na fila do Service Bus. O comprimento da fila do autocarro de serviço é uma métrica especial e o limiar é o número de mensagens por exemplo. Por exemplo, se houver duas instâncias e se o limiar for fixado para 100, o escalonamento ocorre quando o número total de mensagens na fila é de 200. Podem ser 100 mensagens por exemplo, 120 e 80, ou qualquer outra combinação que acrescente até 200 ou mais.

Para conjuntos de escala VM, pode atualizar a definição de Autoescala no modelo de Gestor de Recursos para utilizar o *nome métrico* como *ApproximateMessageCount* e passar o ID da fila de armazenamento como *metricResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para o Service Bus, o conceito de grupo de recursos não existe, mas o Azure Resource Manager cria um grupo de recursos predefinido por região. O grupo de recursos encontra-se normalmente no formato 'Default-ServiceBus-[região]. Por exemplo, 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast', etc.
>
>