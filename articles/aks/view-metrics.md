---
title: Ver métricas de cluster para serviço Azure Kubernetes (AKS)
description: Ver métricas de cluster para serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969508"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Ver métricas de cluster para serviço Azure Kubernetes (AKS)

AKS fornece um conjunto de métricas para o plano de controlo, incluindo o Servidor API e o autoescalador de cluster, e nós de cluster. Estas métricas permitem monitorizar a saúde do seu cluster e resolver problemas. Pode ver as métricas do seu cluster utilizando o portal Azure.

> [!NOTE]
> Estas métricas de cluster AKS sobrepõem-se a um subconjunto das [métricas fornecidas pela Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Veja as métricas do seu cluster AKS utilizando o portal Azure

Para ver as métricas do seu cluster AKS:

1. Inscreva-se no [portal Azure][azure-portal] e navegue para o seu cluster AKS.
1. No lado esquerdo sob *monitorização,* selecione *Métricas*.
1. Crie um gráfico para as métricas que pretende ver. Por exemplo, criar um gráfico:
    1. Para *Scope,* escolha o seu cluster.
    1. Para *o Espaço Métrico de Nome,* escolha *métricas padrão de serviço de contentores (geridos).*
    1. Para *métrica*, em *Pods* escolha *Número de Cápsulas por fase*.
    1. Para *agregação* escolha *Avg*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

O exemplo acima mostra as métricas para o número médio de cápsulas para o *myAKSCluster*.

## <a name="available-metrics"></a>Métricas disponíveis

Estão disponíveis as seguintes métricas de cluster:

| Name | Group | ID | Description |
| --- | --- | --- | ---- |
| Pedidos de voo | Servidor API (pré-visualização) |apiserver_current_inflight_requests | Número máximo de pedidos de voo atualmente ativos no servidor API por pedido. |
| Estado de Funcionamento do Cluster | Cluster Autoscaler (pré-visualização) | cluster_autoscaler_cluster_safe_to_autoscale | Determina se o autoescala de cluster irá ou não tomar medidas no cluster. |
| Escala para baixo Cooldown | Cluster Autoscaler (pré-visualização) | cluster_autoscaler_scale_down_in_cooldown | Determina se a balança para baixo está em arrefecimento - Nenhum nós será removido durante este período de tempo. |
| Nódes Não-Estimados | Cluster Autoscaler (pré-visualização) | cluster_autoscaler_unneeded_nodes_count | O cluster auotscaler marca esses nós como candidatos à eliminação e são eventualmente eliminados. |
| Cápsulas insoquiáveis | Cluster Autoscaler (pré-visualização) | cluster_autoscaler_unschedulable_pods_count | Número de cápsulas que não são atualmente não-me identificantes no cluster. |
| Número total de núcleos cpu disponíveis num cluster gerido | Nós | kube_node_status_allocatable_cpu_cores | Número total de núcleos de CPU disponíveis num cluster gerido. |
| Quantidade total de memória disponível num cluster gerido | Nós | kube_node_status_allocatable_memory_bytes | Quantidade total de memória disponível num cluster gerido. |
| Estados para várias condições de nó | Nós | kube_node_status_condition | Estados para várias condições de nó |
| Millicores de Utilização cpu | Nós (pré-visualização) | node_cpu_usage_millicores | Medição agregada da utilização do CPU em millicores através do cluster. |
| Percentagem de Utilização cpu | Nós (pré-visualização) | node_cpu_usage_percentage | Utilização média agregada do CPU medida em percentagem em todo o cluster. |
| Memória RSS Bytes | Nós (pré-visualização) | node_memory_rss_bytes | Memória RSS do contentor utilizada nos bytes. |
| Percentagem RSS memória | Nós (pré-visualização) | node_memory_rss_percentage | Memória RSS do contentor usada em percentagem. |
| Conjunto de trabalho de memória bytes | Nós (pré-visualização) | node_memory_working_set_bytes | Memória do conjunto de trabalho do recipiente utilizada nos bytes. |
| Percentagem de conjunto de trabalho de memória | Nós (pré-visualização) | node_memory_working_set_percentage | Memória do conjunto de trabalho do contentor usado em por cento. |
| Bytes usados em disco | Nós (pré-visualização) | node_disk_usage_bytes | Espaço de disco usado em bytes por dispositivo. |
| Percentagem usada em disco | Nós (pré-visualização) | node_disk_usage_percentage | Espaço de disco usado por cento por dispositivo. |
| Rede em bytes | Nós (pré-visualização) | node_network_in_bytes | Rede recebeu bytes. |
| Network out Bytes | Nós (pré-visualização) | node_network_out_bytes | Bytes transmitidos em rede. |
| Número de cápsulas no estado de Ready | Casulos | kube_pod_status_ready | Número de cápsulas no estado *de Ready.* |
| Número de cápsulas por fase | Casulos | kube_pod_status_phase | Número de cápsulas por fase. |

> [!IMPORTANT]
> As métricas em pré-visualização podem ser atualizadas ou alteradas, incluindo os seus nomes e descrições, enquanto estão em pré-visualização.

## <a name="next-steps"></a>Passos seguintes

Além das métricas de cluster para AKS, também pode utilizar o Azure Monitor com o seu cluster AKS. Para obter mais informações sobre a utilização do Azure Monitor com AKS, consulte [o Azure Monitor para obter recipientes][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/