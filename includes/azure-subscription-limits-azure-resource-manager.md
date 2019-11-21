---
title: incluir ficheiro
description: incluir ficheiro
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a01455da36e8ab573773fec2a6da1f7903b8ea20
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224499"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| VMs por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |25,000<sup>1</sup> per region. |25,000 per region. |
| Total de núcleos de VM por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Contacte o suporte. |
| VM per series, such as Dv2 and F, cores per [subscription](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region. | Contacte o suporte. |
| [Coadministrators](../articles/billing-add-change-azure-subscription-administrator.md) per subscription |Unlimited. |Unlimited. |
| [Storage accounts](../articles/storage/common/storage-quickstart-create-account.md) per region per subscription |250 |250 |
| [Resource groups](../articles/azure-resource-manager/resource-group-overview.md) per subscription |980 |980 |
| [Availability sets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per subscription |2,000 per region. |2,000 per region. |
| Azure Resource Manager API request size |4,194,304 bytes. |4,194,304 bytes. |
| Tags per subscription<sup>2</sup> |Unlimited. |Unlimited. |
| Unique tag calculations per subscription<sup>2</sup> | 10,000 | 10,000 |
| [Serviços cloud](../articles/cloud-services/cloud-services-choose-me.md) por subscrição |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por subscrição |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Subscription-level deployments](../articles/azure-resource-manager/deploy-to-subscription.md) per location | 800<sup>4</sup> | 800 |

<sup>1</sup>Default limits vary by offer category type, such as Free Trial and Pay-As-You-Go, and by series, such as Dv2, F, and G. For example, the default for Enterprise Agreement subscriptions is 350.

<sup>2</sup>You can apply an unlimited number of tags per subscription. The number of tags per resource or resource group is limited to 50. Resource Manager returns a [list of unique tag name and values](/rest/api/resources/tags) in the subscription only when the number of tags is 10,000 or less. You still can find a resource by tag when the number exceeds 10,000.  

<sup>3</sup>These features are no longer required with Azure resource groups and Resource Manager.

<sup>4</sup>If you reach the limit of 800 deployments, delete deployments from the history that are no longer needed. To delete subscription level deployments, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) or [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Virtual machine cores have a regional total limit. They also have a limit for regional per-size series, such as Dv2 and F. These limits are separately enforced. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. This subscription can deploy 30 A1 VMs, or 30 D1 VMs, or a combination of the two not to exceed a total of 30 cores. An example of a combination is 10 A1 VMs and 20 D1 VMs.  
> <!-- -->
> 
> 

