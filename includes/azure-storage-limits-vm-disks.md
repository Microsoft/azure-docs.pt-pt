---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261385"
---
Pode anexar vários discos de dados para uma máquina virtual do Azure. Com base nos destinos de escalabilidade e desempenho para discos de dados de uma VM, é possível determinar o número e tipo de disco que tem de cumprir os requisitos de capacidade e desempenho.

> [!IMPORTANT]
> Para otimizar o desempenho, limite o número de discos muito utilizados anexados à máquina virtual para evitar uma possível limitação. Se todos os discos ligados não são muito utilizados ao mesmo tempo, a máquina virtual pode suportar um grande número de discos.

**Para o Azure discos geridos:**

A tabela seguinte ilustra o padrão e os limites máximos do número de recursos por região por subscrição

> | Recurso | Limite predefinido  | Limite máximo |
> | --- | --- | --- |
> | Discos geridos Standard | 25,000 | 50,000 |
> | Discos geridos Standard SSD | 25,000 | 50,000 |
> | Discos geridos Premium | 25,000 | 50,000 |
> | Instantâneos de Standard_LRS | 25,000 | 50,000 |
> | Instantâneos de Standard_ZRS | 25,000 | 50,000 |
> | Imagem gerida | 25,000 | 50,000 |

* **Para contas de armazenamento Standard:** Uma conta de armazenamento Standard tem uma taxa de pedidos total máxima de 20.000 IOPS. O IOPS total em todos os discos da máquina virtual numa conta de armazenamento Standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos muito utilizados suportados por uma conta de armazenamento Standard individual com base no limite de taxa de pedido. Por exemplo, para uma VM de escalão básico, o número máximo de discos muito utilizados é cerca de 66, que é 20.000/300 IOPS por disco. O número máximo de discos muito utilizados para uma VM de escalão Standard é cerca de 40, que é 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

