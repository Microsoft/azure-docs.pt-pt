---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: cd3ff3fce80e66d7cd61636b4416cb2fc28f5e77
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956508"
---
| Recurso | Limite |
| --- | --- |
| VMs por [subscrição](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> por região. |
| Total de núcleos de VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| Núcleos totais de Azure Spot VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| VM por série, como Dv2 e F, núcleos por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por subscrição |2.500 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| [Grupos de colocação de proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Certificados por conjunto de disponibilidade | 199<sup>2</sup> |
| Certificados por subscrição |Ilimitado<sup>3</sup> |

<sup>1</sup> Os limites predefinidos variam por tipo de categoria de oferta, tais como Teste Gratuito e Pay-As-You-Go, e por séries, tais como Dv2, F e G. Por exemplo, o padrão para as subscrições do Enterprise Agreement é de 350.

<sup>2</sup> Propriedades como as chaves públicas SSH também são empurradas como certificados e contam para este limite. Para ultrapassar este limite, utilize a [extensão Azure Key Vault para Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows) ou a [extensão Azure Key Vault para o Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) instalar certificados.

<sup>3</sup> Com o Azure Resource Manager, os certificados são armazenados no Cofre da Chave Azure. O número de certificados é ilimitado para uma subscrição. Há um limite de 1-MB de certificados por implantação, que consiste em um único VM ou um conjunto de disponibilidade.



> [!NOTE]
> Os núcleos de máquinas virtuais têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Estes limites são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição pode implantar 30 VMs A1, ou 30 D1 VMs, ou uma combinação dos dois não exceder um total de 30 núcleos. Um exemplo de uma combinação é 10 VMs A1 e 20 D1 VMs.  
> <!-- -->
>
