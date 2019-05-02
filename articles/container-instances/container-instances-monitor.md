---
title: Monitorizar contentores no Azure Container Instances
description: Como monitorizar o consumo dos recursos de computação, como CPU e memória por seus contentores no Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570941"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorizar recursos de contentores no Azure Container Instances

[O Azure Monitor] [ azure-monitoring] fornece informações sobre os recursos de computação utilizados por suas instâncias de contentores. Estes dados de utilização de recursos ajudam-o a determinar as definições de recursos melhor para os grupos de contentor. Monitor do Azure também fornece métricas que controlam a atividade de rede nas instâncias de contentor.

Este documento fornece detalhes sobre a coleta de métricas de Monitor do Azure para instâncias de contentor com o portal do Azure e a CLI do Azure.

> [!IMPORTANT]
> Métricas de Monitor do Azure no Azure Container Instances estão atualmente em pré-visualização e algumas [limitações aplicam-se](#preview-limitations). As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Neste momento, as métricas do Azure Monitor só estão disponíveis para contentores do Linux.

## <a name="available-metrics"></a>Métricas disponíveis

O Azure Monitor proporciona as seguintes [métricas do Azure Container Instances][supported-metrics]. Estas métricas estão disponíveis para um grupo de contentor e contentores individuais.

* **Utilização da CPU** - medido em **milinúcleos**. Um millicore é 1/1000th de um núcleo de CPU, pelo que 500 milinúcleos (ou 500 milhões) representa a 50% de utilização de um núcleo de CPU. Agregados como **utilização média** em todos os núcleos.

* **Utilização da memória** - agregada como **médio de bytes**.

* **Bytes recebidos por segundo de rede** e **rede Bytes transmitidos por segundo** - agregada como **médio de bytes por segundo**. 

## <a name="get-metrics---azure-portal"></a>Obter métricas - Portal do Azure

Quando é criado um grupo de contentores, estão disponíveis dados do Azure Monitor no portal do Azure. Para ver as métricas para um grupo de contentores, vá para o **descrição geral** página para o grupo de contentor. Aqui pode ver gráficos previamente criados para cada uma das métricas disponíveis.

![dual-chart][dual-chart]

Num grupo de contentor que contém vários contentores, utilize um [dimensão] [ monitor-dimension] para apresentar métricas por contentor. Para criar um gráfico com métricas de contentores individuais, execute os seguintes passos:

1. Na **descrição geral** página, selecione um dos gráficos de métricas, como **CPU**. 
1. Selecione o **aplicam-se de que a divisão** botão e selecione **nome do contentor**.

![dimensão][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas - CLI do Azure

Métricas do container instances também podem ser coletadas com a CLI do Azure. Primeiro, obtenha o ID do grupo de contentores com o seguinte comando. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contentores.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilize o seguinte comando para obter as métricas de utilização da **CPU**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Alterar o valor do `--metric` parâmetro no comando para obter outra [suportado métricas][supported-metrics]. Por exemplo, utilize o seguinte comando para obter **memória** métrica de utilização. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

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

Para um grupo de vários contentor, o `containerName` dimensão pode ser adicionada para devolver as métricas por contentor.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

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

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Azure Monitor na [Descrição geral do Azure Monitor][azure-monitoring].

Saiba como criar [alertas de métricas] [ metric-alert] para ser notificado quando uma métrica do Azure Container Instances ultrapassa um limiar.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
