---
title: Monitorizar as instâncias do contentor
description: Como monitorizar o consumo de recursos computacional como CPU e memória pelos seus recipientes em Instâncias de Contentores Azure.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: ae9725ffe66bebbed26745c311b2ada07d5d2c00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589293"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorizar recursos de contentores no Azure Container Instances

[O Azure Monitor][azure-monitoring] fornece informações sobre os recursos computacional utilizados pelos seus contentores. Estes dados de utilização de recursos ajudam-no a determinar as melhores definições de recursos para os seus grupos de contentores. O Azure Monitor também fornece métricas que rastreiam a atividade da rede nas suas instâncias de contentores.

Este documento detalha a recolha de métricas do Azure Monitor para casos de contentores utilizando o portal Azure e o Azure CLI.

> [!IMPORTANT]
> As métricas do Monitor Azure em Instâncias de Contentores Azure estão atualmente em pré-visualização, e [algumas limitações se aplicam](#preview-limitations). As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Neste momento, as métricas do Azure Monitor só estão disponíveis para recipientes Linux.

## <a name="available-metrics"></a>Métricas disponíveis

O Azure Monitor fornece as [seguintes métricas para as instâncias do contentor Azure][supported-metrics]. Estas métricas estão disponíveis para um grupo de contentores e contentores individuais. Por padrão, as métricas são agregadas como médias.

- **Utilização do CPU** medida em **millicores**. 
  - Um millicore é 1/1000º de um núcleo de CPU, pelo que 500 millicores representam o uso de 0,5 núcleo cpu.
- **Utilização da memória** em bytes
- **Bytes de rede recebidos** por segundo
- **Bytes de rede transmitidos** por segundo 

## <a name="get-metrics---azure-portal"></a>Obter métricas - Portal do Azure

Quando é criado um grupo de contentores, estão disponíveis dados do Azure Monitor no portal do Azure. Para ver as métricas de um grupo de contentores, vá à página **geral** do grupo de contentores. Aqui pode ver gráficos pré-criados para cada uma das métricas disponíveis.

![dual-chart][dual-chart]

Num grupo de contentores que contém vários recipientes, utilize uma [dimensão][monitor-dimension] para mostrar métricas por recipiente. Para criar um gráfico com métricas de contentores individuais, execute os seguintes passos:

1. Na página **'Visão Geral',** selecione um dos gráficos métricos, como **o CPU**. 
1. Selecione o botão **de divisão Aplicar** e selecione Nome do **Recipiente**.

![A captura do ecrã mostra as métricas de uma instância de recipiente com a divisão selecionada e o nome do recipiente selecionado.][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas - CLI do Azure

As métricas para casos de contentores também podem ser recolhidas utilizando o CLI Azure. Primeiro, obtenha o ID do grupo de contentores com o seguinte comando. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contentores.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilize o seguinte comando para obter as métricas de utilização da **CPU**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Altere o valor do `--metric` parâmetro no comando para obter [outras métricas suportadas][supported-metrics]. Por exemplo, utilize o seguinte comando para obter métricas de utilização da **memória.** 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Para um grupo multi-contentores, a `containerName` dimensão pode ser adicionada às métricas de retorno por recipiente.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Monitor na [Descrição geral do Azure Monitor][azure-monitoring].

Aprenda a criar [alertas métricos][metric-alert] para ser notificado quando uma métrica para instâncias de contentores Azure cruza um limiar.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
