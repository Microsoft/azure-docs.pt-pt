---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: a9c830025b9ce6c93bf106a8348b0f9dc6521582
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561013"
---
| Recurso | Limite |
| --- | --- |
| VMs por [subscrição](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> por região. |
| Total de núcleos de VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| Núcleos totais de Azure Spot VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| VM por série, como Dv2 e F, núcleos por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por subscrição |2.500 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| Certificados por subscrição |Ilimitado<sup>2</sup> |

<sup>1</sup> Os limites predefinidos variam por tipo de categoria de oferta, tais como Teste Gratuito e Pay-As-You-Go, e por séries, tais como Dv2, F e G. Por exemplo, o padrão para as subscrições do Enterprise Agreement é de 350.

<sup>2</sup> Com o Azure Resource Manager, os certificados são armazenados no Cofre da Chave Azure. O número de certificados é ilimitado para uma subscrição. Há um limite de 1-MB de certificados por implantação, que consiste em um único VM ou um conjunto de disponibilidade.

> [!NOTE]
> Os núcleos de máquinas virtuais têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Estes limites são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição pode implantar 30 VMs A1, ou 30 D1 VMs, ou uma combinação dos dois não exceder um total de 30 núcleos. Um exemplo de uma combinação é 10 VMs A1 e 20 D1 VMs.  
> <!-- -->
>