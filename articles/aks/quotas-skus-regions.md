---
title: Limites para recursos, SKUs, regiões
titleSuffix: Azure Kubernetes Service
description: Conheça as quotas padrão, os tamanhos de VM SKU restritos e a disponibilidade da região do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9592b03f5bd9e9436db5665e040b4afd3b68b764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562740"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quotas, restrições de tamanho de máquina virtual e disponibilidade da região no Serviço Azure Kubernetes (AKS)

Todos os serviços da Azure estabelecem limites e quotas de incumprimento para recursos e funcionalidades. Certos SKUs de máquina virtual (VM) também são restritos para utilização.

Este artigo detalha os limites de recursos padrão para os recursos do Serviço Azure Kubernetes (AKS) e a disponibilidade de AKS nas regiões de Azure.

## <a name="service-quotas-and-limits"></a>Quotas e limites do serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura de aprovisionamento

Aplicam-se todas as outras limitações de rede, computação e armazenamento para a infraestrutura de aprovisionamento. Para os limites relevantes, consulte os [limites de subscrição e serviço da Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!IMPORTANT]
> Quando atualiza um cluster AKS, os recursos adicionais são temporariamente consumidos. Estes recursos incluem endereços IP disponíveis numa sub-rede de rede virtual ou quota vCPU de máquina virtual. Se utilizar os recipientes do Windows Server, a única abordagem endossada para aplicar as atualizações mais recentes nos nós é realizar uma operação de atualização. Um processo falhado de atualização do cluster pode indicar que você não tem o espaço de endereço IP disponível ou quota vCPU para lidar com estes recursos temporários. Para obter mais informações sobre o processo de atualização do nó do servidor do Windows, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó num cluster AKS contém uma quantidade fixa de recursos computacional, tais como vCPU e memória. Se um nó AKS contiver recursos computacionais insuficientes, as cápsulas podem não funcionar corretamente. Para garantir que as cápsulas *de sistema kube* e as suas aplicações podem ser agendadas de forma fiável, **não utilize os seguintes SKUs VM em AKS:**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre os tipos de VM e os seus recursos de computação, consulte [tamanhos para máquinas virtuais em Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a mais recente lista de onde pode implantar e executar clusters, consulte [a disponibilidade da região AKS.][region-availability]

## <a name="next-steps"></a>Passos seguintes

Podem ser aumentados determinados limites e quotas predefinidos. Se o seu recurso suportar um aumento, solicite o aumento através de um [pedido de suporte Azure][azure-support] (para **tipo de emissão,** **selecione Quota).**

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
