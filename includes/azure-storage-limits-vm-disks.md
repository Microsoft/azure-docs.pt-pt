---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669790"
---
Você pode anexar um número de discos de dados a uma máquina virtual do Azure. Com base nas metas de escalabilidade e desempenho dos discos de dados de uma VM, você pode determinar o número e o tipo de disco necessários para atender aos requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para obter um desempenho ideal, limite o número de discos altamente utilizados anexados à máquina virtual para evitar uma limitação possível. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual poderá dar suporte a um número maior de discos.

**Para Azure Managed disks:**

A tabela a seguir ilustra os limites padrão e máximo do número de recursos por região por assinatura. Não há nenhum limite para o número de Managed Disks, instantâneos e imagens por grupo de recursos.  

> | Resource | Limite predefinido  | Limite máximo |
> | --- | --- | --- |
> | Managed disks Standard | 50,000 | 50,000 |
> | Discos geridos Standard SSD | 50,000 | 50,000 |
> | Discos geridos Premium | 50,000 | 50,000 |
> | Instantâneos do Standard_LRS | 50,000 | 50,000 |
> | Instantâneos do Standard_ZRS | 50,000 | 50,000 |
> | Imagem gerenciada | 50,000 | 50,000 |

* **Para contas de armazenamento padrão:** Uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. O total de IOPS em todos os discos de máquina virtual em uma conta de armazenamento Standard não deve exceder esse limite.
  
    Você pode calcular aproximadamente o número de discos altamente utilizados com suporte em uma única conta de armazenamento Standard com base no limite da taxa de solicitação. Por exemplo, para uma VM de camada básica, o número máximo de discos altamente utilizados é de cerca de 66, que é de 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para uma VM de camada Standard é de cerca de 40, que é de 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de transferência total máxima de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

