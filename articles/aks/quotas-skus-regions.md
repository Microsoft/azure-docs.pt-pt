---
title: Quotas e disponibilidade das regiões no Azure Kubernetes Service (AKS) SKUs
description: Saiba mais sobre as quotas predefinidas, tamanhos de SKU de VM de nó restrito e disponibilidade das regiões do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901452"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas e restrições de tamanho de máquina virtual e disponibilidade das regiões no Azure Kubernetes Service (AKS)

Todos os serviços Azure definiram limites e quotas para recursos e funcionalidades predefinidos. Determinados SKUs de máquina virtual (VM) também estão restritos para utilização.

Este artigo fornece detalhes sobre os limites de recursos padrão para recursos do Azure Kubernetes Service (AKS) e a disponibilidade do AKS em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Quotas e limites do serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura de aprovisionamento

Aplicam-se todas as outras limitações de rede, computação e armazenamento para a infraestrutura de aprovisionamento. Para os limites relevantes, consulte [subscrição do Azure e limites do serviço](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Ao atualizar um cluster do AKS, recursos adicionais temporariamente são consumidos. Esses recursos incluem endereços IP disponíveis numa sub-rede de rede virtual ou a quota de vCPU de máquina virtual. Se utilizar contentores do Windows Server (atualmente em pré-visualização no AKS), é a única abordagem apoiada para aplicar as atualizações mais recentes para os nós efetuar uma operação de atualização. Um processo de atualização do cluster com falhas pode indicar que não tem o IP endereço espaço ou vCPU quota disponível para lidar com estes recursos temporários. Para obter mais informações sobre o processo de atualização do nó do Windows Server, consulte [atualizar um conjunto de nós no AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó num cluster do AKS contém uma quantidade fixa de recursos de computação, tais como a memória e vCPU. Se um nó AKS contém recursos de computação suficiente, poderão falhar pods para serem executados corretamente. Para garantir que o necessário *kube system* pods e as suas aplicações podem ser agendadas com confiança, não utilize os seguintes SKU de VM no AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre tipos de VM e os recursos de computação, consulte [tamanhos de máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade regional

Para obter a lista mais recente de onde pode implementar e executar clusters, veja [disponibilidade das regiões AKS][region-availability].

## <a name="next-steps"></a>Passos Seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Se o seu recurso oferece suporte a um aumento, solicitar o aumento por meio de um [pedido de suporte do Azure] [ azure-support] (para **tipo de problema**, selecione **Quota** ).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
