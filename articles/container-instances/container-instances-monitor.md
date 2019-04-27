---
title: Monitorizar contentores no Azure Container Instances
description: Detalhes sobre como monitorizar o consumo de recursos de computação, como CPU e memória, pelos contentores no Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: 950d8b4b5ec1a55e2054039a01d6807915b5c714
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580174"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorizar recursos de contentores no Azure Container Instances

O Azure Monitor fornece informações sobre os recursos de computação utilizados pelas instâncias de contentores. Utilize o Azure Monitor para controlar a utilização da CPU e da memória dos grupos de contentores e respetivos contentores. Estes dados de utilização de recursos ajudam a determinar as melhores definições de CPU e memória para os grupos de contentores.

Este documento fornece detalhes sobre a recolha de dados de utilização da CPU e da memória das instâncias de contentores através do portal do Azure e da CLI do Azure.

> [!IMPORTANT]
> Neste momento, as métricas de utilização de recursos apenas estão disponíveis para contentores do Linux.
>

## <a name="available-metrics"></a>Métricas disponíveis

O Azure Monitor fornece métricas sobre a utilização da **CPU** e da **memória** para o Azure Container Instances. Ambas as métricas estão disponíveis para um grupo de contentores e contentores individuais.

As métricas de CPU são expressas em **milinúcleos**. Um milinúcleo é 1/1000 de um núcleo de CPU, pelo que 500 milinúcleos (ou 500 m) representam 50% da utilização de um núcleo de CPU.

As métricas de memória são expressas em **bytes**.

## <a name="get-metrics---azure-portal"></a>Obter métricas - Portal do Azure

Quando é criado um grupo de contentores, estão disponíveis dados do Azure Monitor no portal do Azure. Para ver as métricas de um grupo de contentores, selecione o grupo de recursos e, em seguida, o grupo de contentores. Aqui, pode ver gráficos pré-criados com a utilização da CPU e da memória.

![dual-chart][dual-chart]

Se tiver um grupo de contentores com vários contentores, utilize uma [dimensão][monitor-dimension] para apresentar as métricas de cada contentor individual. Para criar um gráfico com métricas de contentores individuais, execute os seguintes passos:

1. Selecione **Monitorizar** no menu de navegação à esquerda.
2. Selecione um grupo de contentores e uma métrica (CPU ou Memória).
3. Selecione o botão de dimensão verde e selecione **Nome do Contentor**.

![dimensão][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas - CLI do Azure

A utilização da CPU e da memória nas instâncias de contentores também pode ser recolhida através da CLI do Azure. Primeiro, obtenha o ID do grupo de contentores com o seguinte comando. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contentores.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilize o seguinte comando para obter as métricas de utilização da **CPU**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Utilize o seguinte comando para obter as métricas de utilização da **memória**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

Para um grupo de vários contentores, pode ser adicionada a dimensão `containerName` para devolver estes dados por contentor.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Azure Monitor na [Descrição geral do Azure Monitor][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
