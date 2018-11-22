---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279856"
---
| Recurso | Limite Predefinido | Limite Máximo |
| --- | --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço cloud<sup>1</sup> |50 |50 |
| Pontos finais por serviço cloud de entrada<sup>2</sup> |150 |150 |

<sup>1</sup>máquinas virtuais criadas no serviço de gestão (em vez do Resource Manager) são armazenadas automaticamente num serviço em nuvem. Pode adicionar mais máquinas virtuais para esse serviço em nuvem para balanceamento de carga e disponibilidade. 

<sup>2</sup>pontos finais de entrada permitir comunicações para uma máquina virtual a partir de fora do serviço de nuvem da máquina virtual. Serviço em nuvem de máquinas virtuais no mesmo ou rede virtual pode automaticamente comunicar entre si. Ver [como configurar pontos finais para uma Máquina Virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
