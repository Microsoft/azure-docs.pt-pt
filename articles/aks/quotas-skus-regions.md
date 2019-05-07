---
title: Quotas, SKU e a disponibilidade de região no Azure Kubernetes Service (AKS)
description: Saiba mais sobre as quotas predefinidas, tamanhos de SKU de VM de nó restrito e disponibilidade das regiões do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072884"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas e restrições de tamanho de máquina virtual e disponibilidade das regiões no Azure Kubernetes Service (AKS)

Todos os serviços do Azure incluem determinados limites e quotas predefinidos para os recursos e funcionalidades. Para o tamanho de nó, determinada máquina virtual (VM) SKUs são, em seguida, restringida para utilização.

Este artigo fornece detalhes sobre os limites de recursos padrão para recursos do Azure Kubernetes Service (AKS), bem como a disponibilidade do serviço AKS em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Quotas e limites do serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura de aprovisionamento

Aplicam-se todas as outras limitações de rede, computação e armazenamento para a infraestrutura de aprovisionamento. Veja [subscrição do Azure e limites de serviço](../azure-subscription-service-limits.md) para os limites relevantes.

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó num cluster do AKS contém uma quantidade fixa de recursos de computação, tais como a memória e vCPU. Se um nó AKS contém recursos de computação suficiente, pods talvez não sejam executados corretamente. Para garantir que o necessário *kube system* confiável podem ser agendadas pods e as suas aplicações, os seguintes SKU de VM não pode ser utilizados no AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre tipos de VM e os recursos de computação, consulte [tamanhos de máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a lista mais recente de onde pode implementar e executar clusters, veja [disponibilidade das regiões AKS][region-availability].

## <a name="next-steps"></a>Passos Seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Para pedir um aumento de um ou mais recursos que suportam tal aumento, submeta um [pedido de suporte do Azure][azure-support] (selecione "Quota" no **Tipo de problema**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
