---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829263"
---
Pode anexar uma série de discos de dados a uma máquina virtual Azure. Com base nos objetivos de escalabilidade e desempenho dos discos de dados de um VM, pode determinar o número e o tipo de disco de que necessita para satisfazer os seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para um melhor desempenho, limite o número de discos altamente utilizados ligados à máquina virtual para evitar possíveis estrangulamentos. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual pode suportar um maior número de discos.

**Para discos geridos pela Azure:**

O quadro a seguir ilustra o incumprimento e os limites máximos do número de recursos por região por subscrição. Não há limite para o número de Discos Geridos, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Discos geridos padrão | 50 000 |
> | Discos geridos standard SSD | 50 000 |
> | Discos geridos premium | 50 000 |
> | Standard_LRS fotos | 50 000 |
> | Standard_ZRS fotos | 50 000 |
> | Imagem gerida | 50 000 |

**Para contas de armazenamento standard:** Uma conta de armazenamento Standard tem uma taxa máxima de pedido total de 20.000 IOPS. O IOPS total em todos os discos de máquinas virtuais numa conta de armazenamento Standard não deve exceder este limite.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa total de produção máxima de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

