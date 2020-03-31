---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335026"
---
| Recurso | Limite |
| --- | --- |
| VMs por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> por região. |
| Total de núcleos de VM por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| Núcleos totais de VM Azure Spot por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| VM por série, tais como Dv2 e F, núcleos por [subscrição](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por subscrição |2.000 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| Certificados por subscrição |Ilimitado<sup>2</sup> |

<sup>1</sup> Os limites padrão variam por tipo de categoria de oferta, como Free Trial e Pay-As-You-Go, e por séries, tais como Dv2, F e G. Por exemplo, o padrão para subscrições do Acordo Empresarial é de 350.

<sup>2</sup> Com o Gestor de Recursos Azure, os certificados são armazenados no Cofre de Chaves Azure. O número de certificados é ilimitado para uma subscrição. Há um limite de 1-MB de certificados por implantação, que consiste num único VM ou num conjunto de disponibilidade.

> [!NOTE]
> Os núcleos de máquinas virtuais têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Estes limites são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição pode implementar 30 VMs A1, ou 30 VMs D1, ou uma combinação dos dois para não exceder um total de 30 núcleos. Um exemplo de uma combinação é 10 VMs A1 e 20 VMs D1.  
> <!-- -->
> 
