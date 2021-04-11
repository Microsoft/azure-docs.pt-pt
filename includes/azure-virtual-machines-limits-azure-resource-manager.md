---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 96c476d1724f9475eb28675fc24e21192935e883
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104719942"
---
| Recurso | Limite |
| --- | --- |
| VMs por [subscrição](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> por região. |
| Total de núcleos de VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| Núcleos totais de Azure Spot VM por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| VM por série, como Dv2 e F, núcleos por [subscrição](https://azure.microsoft.com/pricing/) |20<sup>1</sup> por região. Suporte de contato para aumentar o limite. |
| [Conjuntos de disponibilidade](../articles/virtual-machines/availability-set-overview.md) por subscrição |2.500 por região. |
| Máquinas virtuais por conjunto de disponibilidade | 200 |
| [Grupos de colocação de proximidade](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 |
| Certificados por conjunto de disponibilidade | 199<sup>2</sup> |
| Certificados por subscrição |Ilimitado<sup>3</sup> |

<sup>1</sup> Os limites predefinidos variam por tipo de categoria de oferta, tais como Teste Gratuito e Pay-As-You-Go, e por séries, tais como Dv2, F e G. Por exemplo, o padrão para as subscrições do Enterprise Agreement é de 350. Para a segurança, as subscrições não têm padrão em 20 núcleos para evitar grandes implementações do núcleo. Se precisar de mais núcleos, envie um bilhete de apoio.

<sup>2</sup> Propriedades como as chaves públicas SSH também são empurradas como certificados e contam para este limite. Para ultrapassar este limite, utilize a [extensão Azure Key Vault para Windows](../articles/virtual-machines/extensions/key-vault-windows.md) ou a [extensão Azure Key Vault para o Linux](../articles/virtual-machines/extensions/key-vault-linux.md) instalar certificados.

<sup>3</sup> Com o Azure Resource Manager, os certificados são armazenados no Cofre da Chave Azure. O número de certificados é ilimitado para uma subscrição. Há um limite de 1-MB de certificados por implantação, que consiste em um único VM ou um conjunto de disponibilidade.

> [!NOTE]
> Os núcleos de máquinas virtuais têm um limite total regional. Eles também têm um limite para séries regionais por tamanho, como Dv2 e F. Estes limites são aplicados separadamente. Por exemplo, considere uma subscrição com um limite total de núcleos de VM na região E.U.A. Leste de 30, um limite de núcleos de série A de 30 e um limite de núcleos de série D de 30. Esta subscrição pode implantar 30 VMs A1, ou 30 D1 VMs, ou uma combinação dos dois não exceder um total de 30 núcleos. Um exemplo de uma combinação é 10 VMs A1 e 20 D1 VMs.
> <!-- -->
>
