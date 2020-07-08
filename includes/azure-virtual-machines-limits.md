---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334607"
---
| Recurso | Limite |
| --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço em nuvem<sup>1</sup> |50 |
| Pontos finais de entrada por serviço em nuvem<sup>2</sup> |150 |

<sup>1</sup> As máquinas virtuais criadas utilizando o modelo de implementação clássico em vez do Azure Resource Manager são automaticamente armazenadas num serviço de cloud. Pode adicionar mais máquinas virtuais ao serviço de nuvem para equilibrar e disponibilidade. 

<sup>2</sup> Os pontos finais de entrada permitem comunicações a uma máquina virtual de fora do serviço de nuvem da máquina virtual. As máquinas virtuais no mesmo serviço em nuvem ou rede virtual podem comunicar automaticamente entre si. Para obter mais informações, consulte [como configurar pontos finais para uma máquina virtual.](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 
