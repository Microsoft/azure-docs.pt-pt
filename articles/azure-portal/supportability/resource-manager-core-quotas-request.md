---
title: Gestor de Recursos Azure vCPU aumenta pedidos
description: Gestor de Recursos Azure vCPU aumenta pedidos
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843672"
---
# <a name="quota-increase-requests"></a>Pedidos de aumento de quota

As quotas vCPU do Gestor de Recursos para máquinas virtuais e conjuntos de escala de máquinas virtuais são aplicadas a dois níveis para cada subscrição, em cada região.

O primeiro nível é o limite total regional de vCPUs em todas as séries VM. O segundo nível é o limite por vCPUs série VM, como o vCPUs da série D. Sempre que uma nova máquina virtual deve ser implantada, a soma da utilização nova e existente de VCPUs para essa série VM não deve exceder a quota vCPU aprovada para essa série VM em particular. Além disso, a contagem total de vCPU nova e existente implementada em todas as séries VM não deve exceder a quota total regional vCPUs aprovada para a subscrição. Se uma dessas quotas for excedida, a implantação de VM não será permitida.
Pode solicitar um aumento do limite de quota vCPUs para a série VM do portal Azure. O aumento da quota da série VM aumenta automaticamente o limite total regional de vCPUs pelo mesmo montante.

Quando uma nova subscrição é criada, o total total de vCPUs padrão pode não ser igual à soma de quotas vCPU padrão para todas as séries VM individuais. Este facto pode resultar numa subscrição com quota suficiente para cada série VM individual que pretende implementar. Pode não ter quota suficiente para a Total Regional vCPUs para todas as implantações. Neste caso, terá de apresentar um pedido para aumentar explicitamente o limite total regional de vCPUs. O limite total regional de vCPUs não pode exceder a soma da quota aprovada em todas as séries VM para a região.

Para obter mais informações sobre quotas, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

