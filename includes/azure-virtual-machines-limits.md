---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334607"
---
| Recurso | Limite |
| --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço de nuvem<sup>1</sup> |50 |
| Pontos finais de entrada por serviço na nuvem<sup>2</sup> |150 |

<sup>1</sup> As máquinas virtuais criadas utilizando o modelo de implantação clássico em vez do Azure Resource Manager são automaticamente armazenadas num serviço na nuvem. Pode adicionar mais máquinas virtuais a esse serviço de nuvem para equilibrar e disponibilidade de carga. 

<sup>2</sup> Os pontos finais de entrada permitem comunicações a uma máquina virtual de fora do serviço de nuvem da máquina virtual. As máquinas virtuais no mesmo serviço de nuvem ou rede virtual podem comunicar-se automaticamente entre si. Para mais informações, consulte [Como configurar pontos finais para uma máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
