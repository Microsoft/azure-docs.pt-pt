---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 09b4f94db3464943a8367bfb3ca89f9a88446193
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554178"
---
Pode anexar vários discos de dados para uma máquina virtual do Azure. Com base nos destinos de escalabilidade e desempenho para discos de dados de uma VM, é possível determinar o número e tipo de disco que tem de cumprir os requisitos de capacidade e desempenho.

> [!IMPORTANT]
> Para otimizar o desempenho, limite o número de discos muito utilizados anexados à máquina virtual para evitar uma possível limitação. Se todos os discos ligados não são muito utilizados ao mesmo tempo, a máquina virtual pode suportar um grande número de discos.

* **Para o Azure discos geridos:** 

> | Recurso | Limite predefinido | Limite máximo |
> | --- | --- | --- |
> | Discos geridos Standard | 10,000 | 50,000 |
> | Discos geridos Standard SSD | 10,000 | 50,000 |
> | Discos geridos Premium | 10,000 | 50,000 |
> | Instantâneos de Standard_LRS | 10,000 | 50,000 |
> | Instantâneos de Standard_ZRS | 10,000 | 50,000 |
> | Imagem gerida | 10,000 | 50,000 |

* **Para contas de armazenamento Standard:** Uma conta de armazenamento Standard tem uma taxa de pedidos total máxima de 20.000 IOPS. O IOPS total em todos os discos da máquina virtual numa conta de armazenamento Standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos muito utilizados suportados por uma conta de armazenamento Standard individual com base no limite de taxa de pedido. Por exemplo, para uma VM de escalão básico, o número máximo de discos muito utilizados é cerca de 66, que é 20.000/300 IOPS por disco. O número máximo de discos muito utilizados para uma VM de escalão Standard é cerca de 40, que é 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

