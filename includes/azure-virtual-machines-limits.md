---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67184617"
---
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço de nuvem<sup>1</sup> |50 |50 |
| Pontos de extremidade de entrada por serviço de nuvem<sup>2</sup> |150 |150 |

<sup>1</sup> As máquinas virtuais criadas usando o modelo de implantação clássico em vez de Azure Resource Manager são armazenadas automaticamente em um serviço de nuvem. Você pode adicionar mais máquinas virtuais a esse serviço de nuvem para balanceamento de carga e disponibilidade. 

<sup>2</sup> Os pontos de extremidade de entrada permitem a comunicação com uma máquina virtual fora do serviço de nuvem da máquina virtual. As máquinas virtuais no mesmo serviço de nuvem ou rede virtual podem se comunicar automaticamente entre si. Para obter mais informações, consulte [como configurar pontos de extremidade para uma máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
