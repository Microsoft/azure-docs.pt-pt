---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334595"
---
Pode anexar vários discos de dados a uma máquina virtual Azure. Com base nos alvos de escalabilidade e desempenho dos discos de dados de um VM, pode determinar o número e o tipo de disco que necessita para satisfazer os seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para um desempenho ótimo, limite o número de discos altamente utilizados ligados à máquina virtual para evitar uma possível aceleração. Se todos os discos ligados não forem altamente utilizados ao mesmo tempo, a máquina virtual pode suportar um maior número de discos.

**Para discos geridos pelo Azure:**

O quadro que se segue ilustra o padrão e os limites máximos do número de recursos por região por subscrição. Não existe limite para o número de Discos Geridos, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Discos geridos por padrão | 50 000 |
> | Discos geridos por SSD padrão | 50 000 |
> | Discos geridos premium | 50 000 |
> | Standard_LRS instantâneos | 50 000 |
> | Standard_ZRS instantâneos | 50 000 |
> | Imagem gerida | 50 000 |

* **Para contas de armazenamento standard:** Uma conta de armazenamento Standard tem uma taxa total máxima de pedido de 20.000 IOPS. O total de IOPS em todos os seus discos de máquina virtual numa conta de armazenamento Standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos altamente utilizados suportados por uma única conta de armazenamento Standard com base no limite da taxa de pedido. Por exemplo, para um VM de nível básico, o número máximo de discos altamente utilizados é de cerca de 66, que é de 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para um VM de nível Standard é de cerca de 40, que é de 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa máxima de produção total de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

