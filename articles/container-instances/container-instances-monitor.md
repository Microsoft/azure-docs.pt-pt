---
title: Monitorar instâncias de contêiner
description: Como monitorar o consumo de recursos de computação, como CPU e memória, por seus contêineres nas instâncias de contêiner do Azure.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: bd86161bc7840be599eb5ee9a20f6dbf143f5f22
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533652"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorizar recursos de contentores no Azure Container Instances

[Azure monitor][azure-monitoring] fornece informações sobre os recursos de computação usados pelas instâncias de contêineres. Esses dados de uso de recursos ajudam a determinar as melhores configurações de recursos para seus grupos de contêineres. O Azure Monitor também fornece métricas que controlam a atividade de rede em suas instâncias de contêiner.

Este documento detalha a coleta de Azure Monitor métricas para instâncias de contêiner usando o portal do Azure e CLI do Azure.

> [!IMPORTANT]
> As métricas de Azure Monitor nas instâncias de contêiner do Azure estão atualmente em versão prévia e algumas [limitações se aplicam](#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de visualização

Neste momento, Azure Monitor métricas só estão disponíveis para contêineres do Linux.

## <a name="available-metrics"></a>Métricas disponíveis

Azure Monitor fornece as seguintes [métricas para instâncias de contêiner do Azure][supported-metrics]. Essas métricas estão disponíveis para um grupo de contêineres e contêineres individuais.

* **Uso da CPU** -medido em **milicores**. Um millicore é 1/1000th de um núcleo de CPU; portanto, 500 milicores (ou 500 m) representa 50% de uso de um núcleo de CPU. Agregado como **uso médio** em todos os núcleos.

* **Uso de memória** -agregado como **média de bytes**.

* **Bytes de rede recebidos por segundo** e **bytes de rede transmitidos por segundo** agregados como **média de bytes por segundo**. 

## <a name="get-metrics---azure-portal"></a>Obter métricas - Portal do Azure

Quando é criado um grupo de contentores, estão disponíveis dados do Azure Monitor no portal do Azure. Para ver as métricas de um grupo de contêineres, vá para a página **visão geral** do grupo de contêineres. Aqui você pode ver gráficos pré-criados para cada uma das métricas disponíveis.

![dual-chart][dual-chart]

Em um grupo de contêineres que contém vários contêineres, use uma [dimensão][monitor-dimension] para apresentar métricas por contêiner. Para criar um gráfico com métricas de contentores individuais, execute os seguintes passos:

1. Na página **visão geral** , selecione um dos gráficos de métrica, como **CPU**. 
1. Selecione o botão **aplicar divisão** e selecione nome do **contêiner**.

![dimensão][dimension]

## <a name="get-metrics---azure-cli"></a>Obter métricas - CLI do Azure

Métricas para instâncias de contêiner também podem ser coletadas usando o CLI do Azure. Primeiro, obtenha o ID do grupo de contentores com o seguinte comando. Substitua `<resource-group>` pelo nome do grupo de recursos e `<container-group>` pelo nome do grupo de contentores.


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

Altere o valor do parâmetro `--metric` no comando para obter outras [métricas com suporte][supported-metrics]. Por exemplo, use o comando a seguir para obter as métricas de uso de **memória** . 

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

Para um grupo de vários contêineres, a dimensão `containerName` pode ser adicionada para retornar métricas por contêiner.

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o monitoramento do Azure na [visão geral do monitoramento do Azure][azure-monitoring].

Saiba como criar [alertas de métrica][metric-alert] para ser notificado quando uma métrica para instâncias de contêiner do Azure cruzar um limite.

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
