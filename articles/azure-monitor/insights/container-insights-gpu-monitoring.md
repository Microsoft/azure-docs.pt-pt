---
title: Configure monitorização da GPU com monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como pode configurar a monitorização dos clusters kubernetes com a NVIDIA e a GPU da AMD ativadas com nódos os azure monitor para contentores.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373313"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Configure monitorização da GPU com monitor Azure para contentores

Começando pela versão do agente *ciprod03022019*, o monitor Azure para contentores agente integrado agora suporta a monitorização da UTILIZAção de GPU (unidades de processamento gráfico) em nós de cluster Kubernetes conscientes da GPU, e monitoriza pods/contentores solicitando e usando recursos GPU.

## <a name="supported-gpu-vendors"></a>Fornecedores de GPU suportados

O Monitor Azure para Contentores suporta a monitorização de clusters de GPU dos seguintes fornecedores de GPU:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

O Monitor Azure para contentores começa automaticamente a monitorizar a utilização de GPU em nós, e a GPU solicita cápsulas e cargas de trabalho recolhendo as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics:**

|Nome da métrica |Dimensão métrica (tags) |Descrição |
|------------|------------------------|------------|
|contentorGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, contentorName, gpuId, gpuModel, gpuVendor|Percentagem de tempo durante o período da amostra passada (60 segundos) durante o qual a GPU estava ocupada/ativamente a processar um recipiente. O ciclo de serviço é um número entre 1 e 100. |
|contentorGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, nome do contentor |Cada recipiente pode especificar limites como uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU. |
|contentorGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, nome do contentor |Cada recipiente pode solicitar uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU.|
|contentorGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, contentorName, gpuId, gpuModel, gpuVendor |Quantidade de Memória GPU em bytes disponíveis para utilização para um recipiente específico. |
|recipienteGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, contentorName, gpuId, gpuModel, gpuVendor |Quantidade de Memória GPU em bytes utilizados por um recipiente específico. |
|nodeGpuAllocatável |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número de GPUs num nó que pode ser usado por Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número total de GPUs num nó. |

## <a name="gpu-performance-charts"></a>Gráficos de desempenho da GPU 

O Monitor Azure para contentores inclui gráficos pré-configurados para as métricas listadas anteriormente na tabela como um livro de GPU para cada cluster. Você pode encontrar o gpu do nó de gpu do livro **GPU gpu** da GPU diretamente de um cluster AKS, selecionando Livros de **Trabalho** do painel da mão esquerda, e da lista de drop-down da **View Workbooks** no Insight.

## <a name="next-steps"></a>Passos seguintes

- Consulte [as GPUs de utilização para cargas de trabalho intensivas em cálculos no Serviço Azure Kubernetes](../../aks/gpu-cluster.md) (AKS) para aprender a implantar um cluster AKS que inclui nós ativados por GPU.

- Saiba mais sobre [GPU Optimized VM SKUs no Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Reveja o [suporte da GPU em Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) para saber mais sobre o apoio experimental da Kubernetes para gerir as GPUs através de um ou mais nós num cluster.