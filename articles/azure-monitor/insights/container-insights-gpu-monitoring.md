---
title: Configure a monitorização da GPU com o Azure Monitor para contentores Microsoft Docs
description: Este artigo descreve como pode configurar os aglomerados kubernetes de monitorização com gPIA e GPU da AMD ativados com Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373313"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Configure a monitorização da GPU com o Monitor Azure para contentores

Começando pela versão do agente *ciprod0302019,* o monitor Azure para contentores integrados agora suporta a monitorização da utilização de GPU (unidades de processamento gráfico) nos nós de cluster Kubernetes conscientes da GPU, e monitor de cápsulas/contentores solicitando e usando recursos gpu.

## <a name="supported-gpu-vendors"></a>Fornecedores de GPU apoiados

O Azure Monitor for Containers suporta a monitorização dos clusters gpu dos seguintes fornecedores de GPU:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

O Azure Monitor para contentores inicia automaticamente a monitorização da utilização da GPU nos nos géis e a GPU solicitando cápsulas e cargas de trabalho, recolhendo as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics:**

|Nome da métrica |Dimensão métrica (etiquetas) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Percentagem de tempo durante o período de amostragem anterior (60 segundos) durante o qual a GPU estava ocupada/ativamente a processar para um recipiente. O ciclo de serviços é um número entre 1 e 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, nome de contentor |Cada recipiente pode especificar limites como uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, nome de contentor |Cada recipiente pode solicitar uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantidade de memória GPU em bytes disponíveis para um recipiente específico. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantidade de memória GPU em bytes utilizados por um recipiente específico. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número de GPUs num nó que pode ser usado por Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número total de GPUs num nó. |

## <a name="gpu-performance-charts"></a>Gráficos de desempenho da GPU 

O Azure Monitor para contentores inclui gráficos pré-configurados para as métricas listadas anteriormente na tabela como um livro de GPU para cada cluster. Pode encontrar o **GDE GU** do livro da GPU diretamente de um cluster AKS, selecionando Livros de **Trabalho** a partir do painel de esquerda e da lista de drop-down do **View Workbooks** no Insight.

## <a name="next-steps"></a>Passos seguintes

- Consulte [as GPUs de utilização para cargas de trabalho intensivas em Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) para aprender a implementar um cluster AKS que inclui nós ativados por GPU.

- Saiba mais sobre [GPU Optimized VM SKUs no Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Reveja [o suporte da GPU em Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) para saber mais sobre o apoio experimental de Kubernetes para gerir GPUs em um ou mais nós em um cluster.