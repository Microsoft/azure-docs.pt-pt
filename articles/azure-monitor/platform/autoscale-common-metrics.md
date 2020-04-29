---
title: Métricas comuns de escala automática
description: Saiba quais as métricas que são comumente usadas para autodimensionar os seus Serviços de Cloud, Máquinas Virtuais e Aplicações Web.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76845574"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comuns de autoscalcificação do Monitor Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A autoscalcificação Do Azure Monitor permite-lhe escalar o número de instâncias de execução para cima ou para baixo, com base em dados de telemetria (métricas). Este documento descreve métricas comuns que você pode querer usar. No portal Azure, pode escolher a métrica do recurso à escala. No entanto, também pode escolher qualquer métrica de um recurso diferente para escalar.

A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Outros serviços Azure utilizam diferentes métodos de escala.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Métricas computadas para VMs baseados em Gestor de Recursos
Por padrão, as máquinas virtuais baseadas em Recursos e conjuntos de escala de máquina virtual emitem métricas básicas (nível de anfitrião). Além disso, quando configura a recolha de dados de diagnóstico para um VM E VM Azure, a extensão de diagnóstico Azure também emite contadores de desempenho de hóspedes-OS (vulgarmente conhecidos como "métricas de guest-OS").  Usas todas estas métricas em regras de escala automática.

Pode utilizar `Get MetricDefinitions` o API/PoSH/CLI para visualizar as métricas disponíveis para o seu recurso VMSS.

Se estiver a usar conjuntos de escala VM e não vir uma determinada métrica listada, então é provável que seja *desativada* na extensão de diagnóstico.

Se uma determinada métrica não estiver a ser amostrada ou transferida na frequência que deseja, pode atualizar a configuração de diagnóstico.

Se qualquer um dos casos anteriores for verdadeiro, reveja o PowerShell para permitir que o [Azure Diagnostics numa máquina virtual que execute o Windows](../../virtual-machines/extensions/diagnostics-windows.md) sobre o PowerShell para configurar e atualizar a extensão de Diagnóstico Azure VM para ativar a métrica. Este artigo também inclui um ficheiro de configuração de diagnóstico de amostras.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas hospedeiras para Windows baseados em Recursos e VMs Linux
As seguintes métricas de nível de anfitrião são emitidas por padrão para O VM e VMSS azure em ambos os casos Windows e Linux. Estas métricas descrevem o seu VM Azure, mas são recolhidos do anfitrião Azure VM em vez de através de um agente instalado no VM convidado. Pode utilizar estas métricas em regras autoscalcificadas.

- [Métricas hospedeiras para Windows baseados em Recursos e VMs Linux](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Métricas hospedeiras para conjuntos de escala seleções de Windows e VM baseados em Recursos](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Métricas de OS convidados para VMs baseados em Gestor de Recursos
Quando cria um VM em Azure, o diagnóstico é ativado utilizando a extensão de Diagnóstico. A extensão de diagnóstico emite um conjunto de métricas retiradas do interior do VM. Isto significa que pode ser automaticamente descalcificada de métricas que não são emitidas por padrão.

Pode gerar uma lista das métricas utilizando o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode criar um alerta para as seguintes métricas:

| Nome métrico | Unidade |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentagem |
| \Processador(_Total)\% Tempo Privilegiado |Percentagem |
| \Tempo de\% utilização do processador(_Total) |Percentagem |
| \Informações do processador(_Total)\Frequência do processador |Contagem |
| \Sistema\Processos |Contagem |
| \Process (_Total)\Contagem de fios |Contagem |
| \Process(_Total)\Contagem de manuseamento |Contagem |
| \Bytes comprometidos de memória\% em uso |Percentagem |
| \Memory\Available Bytes |Bytes |
| \Memória\Bytes Comprometidos |Bytes |
| \Memory\Commit Limit |Bytes |
| \Memory\Pool Paged Bytes |Bytes |
| \Memory\Pool Bytes Não Paged |Bytes |
| \Tempo de disco\% de disco de disco físico(_Total) |Percentagem |
| \PhysicalDisk(_Total)\% Tempo de leitura do disco |Percentagem |
| \Tempo de escrita\% do disco de Disco físico (_Total) |Percentagem |
| \PhysicalDisk(_Total)\Transferências de disco/seg |CondeEmSegundo |
| \PhysicalDisk(_Total)\Leituras/seg de disco |CondeEmSegundo |
| \PhysicalDisk(_Total)\Disk Writes/seg |CondeEmSegundo |
| \PhysicalDisk(_Total)\Discos Bytes/seg |BytesPerSecond |
| \PhysicalDisk(_Total)\Disco Lido Bytes/seg |BytesPerSecond |
| \PhysicalDisk(_Total)\Disk Write Bytes/seg |BytesPerSecond |
| \PhysicalDisk(_Total)\Avg. Comprimento da fila do disco |Contagem |
| \PhysicalDisk(_Total)\Avg. Disk Ler comprimento da fila |Contagem |
| \PhysicalDisk(_Total)\Avg. Disk Write Queue Length |Contagem |
| \Espaço Livre de\% \LogicalDisk(_Total) |Percentagem |
| \LogicalDisk(_Total)\Free Megabytes |Contagem |

### <a name="guest-os-metrics-linux-vms"></a>Métricas de Os Linux
Quando cria um VM em Azure, o diagnóstico é ativado por padrão utilizando a extensão de Diagnóstico.

Pode gerar uma lista das métricas utilizando o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Pode criar um alerta para as seguintes métricas:

| Nome métrico | Unidade |
| --- | --- |
| \Memória\Memória Disponível |Bytes |
| \Memory\PercentAvailableMemory |Percentagem |
| \Memória\Memória Usada |Bytes |
| \Memória\Memória Por Ter |Percentagem |
| \Memory\PercentUsedByCache |Percentagem |
| \Memory\PagesPerSec |CondeEmSegundo |
| \Memory\PagesReadPerSec |CondeEmSegundo |
| \Memory\PagesWrittenPerSec |CondeEmSegundo |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percentagem |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percentagem |
| \Processador\PercentidleTime |Percentagem |
| \Processador\PercentuserTime |Percentagem |
| \Processador\PercentNiceTime |Percentagem |
| \Processador\PercentPrivilegedTime |Percentagem |
| \Processador\PercentinterruptTime |Percentagem |
| \Processador\PercentdPCTime |Percentagem |
| \Processador\PercentProcessorTime |Percentagem |
| \processador\PercentiowaitTime |Percentagem |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CondeEmSegundo |
| \Physicaldisk\ReadsperSecond |CondeEmSegundo |
| \PhysicalDisk\WritesPerSecond |CondeEmSegundo |
| \Physicaldisk\AverageReadTime |Segundos |
| \Physicaldisk\AverageWriteTime |Segundos |
| \PhysicalDisk\Tempo médio de transferência |Segundos |
| \Physicaldisk\AveragediskQueueLength |Contagem |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitido |Contagem |
| \NetworkInterface\PacketsReceived |Contagem |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Contagem |
| \NetworkInterface\TotalTxErrors |Contagem |
| \NetworkInterface\TotalColisões |Contagem |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Métricas do Serviço de Aplicações (Quinta do Servidor) comumente utilizadas
Também pode realizar uma escala automática com base em métricas comuns do servidor web, como o comprimento da fila http. O seu nome métrico é **HttpQueueLength**.  A secção seguinte lista as métricas de exploração de servidores disponíveis (App Service).

### <a name="web-apps-metrics"></a>Métricas de Aplicativos Web
Pode gerar uma lista das métricas das Aplicações Web utilizando o seguinte comando no PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode alertar ou escalar por estas métricas.

| Nome métrico | Unidade |
| --- | --- |
| CpuPercentage |Percentagem |
| Percentagem de Memória |Percentagem |
| Comprimento da fila do disco |Contagem |
| HttpqueueLength |Contagem |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas de armazenamento comumente usadas
Pode escalar por comprimento de fila de armazenamento, que é o número de mensagens na fila de armazenamento. O comprimento da fila de armazenamento é uma métrica especial e o limiar é o número de mensagens por exemplo. Por exemplo, se houver dois casos e se o limiar for fixado para 100, o escalonamento ocorre quando o número total de mensagens na fila é de 200. Podem ser 100 mensagens por exemplo, 120 e 80, ou qualquer outra combinação que acrescente 200 ou mais.

Configure esta definição no portal Azure na lâmina **Definições.** Para conjuntos de escala VM, pode atualizar a definição de escala automática no modelo De Gestor de Recursos para usar *o nome métrico* como *ApproximateMessageCount* e passar o ID da fila de armazenamento como *metricResourceUri*.

Por exemplo, com uma Conta de Armazenamento Clássica, a métrica de definição de escala automáticaTrigger incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Para uma conta de armazenamento (não clássica), a métricaTrigger incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas de ônibus de serviço comumente usados
Pode escalar por comprimento de fila de ônibus de serviço, que é o número de mensagens na fila do Ônibus de serviço. O comprimento da fila do autocarro de serviço é uma métrica especial e o limiar é o número de mensagens por exemplo. Por exemplo, se houver dois casos e se o limiar for fixado para 100, o escalonamento ocorre quando o número total de mensagens na fila é de 200. Podem ser 100 mensagens por exemplo, 120 e 80, ou qualquer outra combinação que acrescente 200 ou mais.

Para conjuntos de escala VM, pode atualizar a definição de escala automática no modelo De Gestor de Recursos para usar *o nome métrico* como *ApproximateMessageCount* e passar o ID da fila de armazenamento como *metricResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para o Service Bus, o conceito do grupo de recursos não existe, mas o Azure Resource Manager cria um grupo de recursos padrão por região. O grupo de recursos encontra-se geralmente no formato 'Default-ServiceBus-[região]. Por exemplo, "Default-ServiceBus-EastUS", "Default-ServiceBus-WestUS", "Default-ServiceBus-AustraliaEast" etc.
>
>

