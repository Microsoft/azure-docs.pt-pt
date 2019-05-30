---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238343"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço cloud<sup>1</sup> |50 |50 |
| Pontos finais por serviço cloud de entrada<sup>2</sup> |150 |150 |

<sup>1</sup>máquinas virtuais criadas com o modelo de implementação clássica em vez do Azure Resource Manager são armazenadas automaticamente num serviço em nuvem. Pode adicionar mais máquinas virtuais para esse serviço em nuvem para balanceamento de carga e disponibilidade. 

<sup>2</sup>pontos finais de entrada permitir comunicações para uma máquina virtual a partir de fora do serviço de nuvem da máquina virtual. Serviço em nuvem de máquinas virtuais no mesmo ou rede virtual pode automaticamente comunicar entre si. Para obter mais informações, consulte [como configurar pontos finais para uma máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
