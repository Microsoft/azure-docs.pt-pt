---
title: Azure Resource Manager vCPU pedidos de aumento de quota
description: Azure Resource Manager vCPU pedidos de aumento de quota
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7fd04428edc28315e714b9e81af0bea0e9344e52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763810"
---
# <a name="quota-increase-requests"></a>Pedidos de aumento de quota

As quotas vCPU do Gestor de Recursos para máquinas virtuais e conjuntos de escala de máquinas virtuais são aplicadas em dois níveis para cada subscrição, em cada região.

O primeiro nível é o limite total regional de vCPUs em todas as séries VM. O segundo nível é o limite de vCPUs da série VM, como os vCPUs da série D. Sempre que uma nova máquina virtual seja implantada, a soma da utilização de vCPUs novas e existentes para esta série VM não deve exceder a quota vCPU aprovada para esta série vM específica. Além disso, a contagem total de vCPU nova e existente implantada em todas as séries VM não deve exceder a quota total regional de vCPUs aprovada para a subscrição. Se uma dessas quotas for excedida, a implantação em VM não será permitida.
Pode solicitar um aumento do limite de quota vCPUs para a série VM a partir do portal Azure. Um aumento do contingente da série VM aumenta automaticamente o limite total regional de vCPUs pelo mesmo montante.

Quando uma nova subscrição é criada, os vCPUs regionais por defeito podem não ser iguais à soma das quotas vCPU padrão para todas as séries VM individuais. Este facto pode resultar numa subscrição com quota suficiente para cada série VM individual que pretende implementar. Pode não ter quota suficiente para os vCPUs regionais totais para todas as implantações. Neste caso, terá de apresentar um pedido para aumentar explicitamente o limite total de vCPUs regional. O limite total de vCPUs regionais não pode exceder a soma da quota aprovada em todas as séries VM para a região.

Para obter mais informações sobre quotas, consulte [as quotas vCPU da máquina virtual](../../virtual-machines/windows/quotas.md) e os [limites de subscrição e serviço da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

