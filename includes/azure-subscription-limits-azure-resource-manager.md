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
ms.openlocfilehash: 2c5312c2e0a7121237c4d11197d90a2a5638557c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021316"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| VMs por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |25.000 por região. |
| Total de núcleos de VM por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Contacte o suporte. |
| Total de núcleos da VM do Azure Spot por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Contacte o suporte. |
| VM por série, como Dv2 e F, núcleos por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Contacte o suporte. |
| [Coadministradores](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) por assinatura |Ilimitado. |Ilimitado. |
| [Contas de armazenamento](../articles/storage/common/storage-account-create.md) por região por assinatura |250 |250 |
| [Grupos de recursos](../articles/azure-resource-manager/management/overview.md) por assinatura |980 |980 |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2\.000 por região. |2\.000 por região. |
| Tamanho da solicitação da API Azure Resource Manager |4\.194.304 bytes. |4\.194.304 bytes. |
| Marcas por assinatura<sup>2</sup> |Ilimitado. |Ilimitado. |
| Cálculos de marca exclusivos por assinatura<sup>2</sup> | 10,000 | 10,000 |
| [Serviços cloud](../articles/cloud-services/cloud-services-choose-me.md) por subscrição |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por subscrição |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Implantações em nível de assinatura](../articles/azure-resource-manager/templates/deploy-to-subscription.md) por local | 800<sup>4</sup> | 800 |

<sup>1</sup> Os limites padrão variam de acordo com o tipo de categoria de oferta, como avaliação gratuita e pago conforme o uso, e por série, como Dv2, F e G. Por exemplo, o padrão para assinaturas de Enterprise Agreement é 350.

<sup>2</sup> Você pode aplicar um número ilimitado de marcas por assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 50. O Resource Manager retorna uma [lista de nomes de marca exclusivos e valores](/rest/api/resources/tags) na assinatura somente quando o número de marcas é de 10.000 ou menos. Você ainda pode encontrar um recurso por marca quando o número excede 10.000.  

<sup>3</sup> Esses recursos não são mais necessários com os grupos de recursos do Azure e o Resource Manager.

<sup>4</sup> Se você atingir o limite de 800 implantações, exclua implantações do histórico que não são mais necessárias. Para excluir implantações de nível de assinatura, use [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Os núcleos de máquina virtual têm um limite de total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Esses limites são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Essa assinatura pode implantar 30 VMs a1 ou 30 VMs D1 ou uma combinação dos dois para não exceder um total de 30 núcleos. Um exemplo de uma combinação é de 10 VMs a1 e 20 VMs D1.  
> <!-- -->
> 
> 

