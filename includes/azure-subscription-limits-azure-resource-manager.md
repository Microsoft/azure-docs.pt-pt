---
title: incluir ficheiro
description: incluir ficheiro
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554018"
---
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| VMs por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |25.000 por região. |
| Total de núcleos de VM por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Contacte o suporte. |
| Núcleos de VM por série, como Dv2 e F, por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. | Contacte o suporte. |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por subscrição |Ilimitado. |Ilimitado. |
| [Contas de armazenamento](../articles/storage/common/storage-quickstart-create-account.md) por região por subscrição |200 |200<sup>2</sup> |
| [Grupos de recursos](../articles/azure-resource-manager/resource-group-overview.md) por subscrição |980 |980 |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por subscrição |2000 por região. |2000 por região. |
| Tamanho do pedido de API do Resource Manager do Azure |4,194,304 bytes. |4,194,304 bytes. |
| Etiquetas por subscrição<sup>3</sup> |Ilimitado. |Ilimitado. |
| Cálculos de etiquetas únicas por subscrição<sup>3</sup> | 10,000 | 10,000 |
| [Serviços cloud](../articles/cloud-services/cloud-services-choose-me.md) por subscrição |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por subscrição |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [Implementações de nível de assinatura](../articles/azure-resource-manager/deploy-to-subscription.md) por localização | 800 | 800 |

<sup>1</sup>limites predefinidos variam por tipo de categoria de oferta, como avaliação gratuita e pay as you go e por série, como Dv2, F e G.

<sup>2</sup>tanto Standard e Premium, as contas de armazenamento estão incluídas. Se precisar de mais de 200 contas de armazenamento, efetue um pedido através de [suporte do Azure](https://azure.microsoft.com/support/faq/). A equipa do armazenamento do Azure analisa o seu caso comercial e pode aprovar até 250 contas de armazenamento.

<sup>3</sup>Pode aplicar um número ilimitado de etiquetas por subscrição. O número de etiquetas por recurso ou grupo de recursos está limitado a 15. Gestor de recursos devolve um [lista de etiquetas únicas nome e os valores](/rest/api/resources/tags) na subscrição apenas quando o número de etiquetas for 10 000 ou inferior. Ainda pode encontrar um recurso por etiqueta quando o número excede 10 000.  

<sup>4</sup>estas funcionalidades já não são necessárias com grupos de recursos do Azure e do Resource Manager.

> [!NOTE]
> Núcleos de máquina virtual tem um limite total regional. Também têm um limite regional por tamanho série, como Dv2 e F. Estes limites são impostos em separado. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região EUA Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição poderá implementar 30 A1 VMs, ou 30 D1 VMs ou uma combinação dos dois não excedam um total de 30 núcleos. Um exemplo de uma combinação é 10 A1 VMs e 20 D1 VMs.  
> <!-- -->
> 
> 

