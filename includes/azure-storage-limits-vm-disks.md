---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334595"
---
Pode anexar uma série de discos de dados a uma máquina virtual Azure. Com base nos objetivos de escalabilidade e desempenho dos discos de dados de um VM, pode determinar o número e o tipo de disco de que necessita para satisfazer os seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para um melhor desempenho, limite o número de discos altamente utilizados ligados à máquina virtual para evitar possíveis estrangulamentos. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual pode suportar um maior número de discos.

**Para discos geridos pela Azure:**

O quadro a seguir ilustra o incumprimento e os limites máximos do número de recursos por região por subscrição. Não há limite para o número de Discos Geridos, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Discos geridos padrão | 50.000 |
> | Discos geridos standard SSD | 50.000 |
> | Discos geridos premium | 50.000 |
> | Standard_LRS fotos | 50.000 |
> | Standard_ZRS fotos | 50.000 |
> | Imagem gerida | 50.000 |

* **Para contas de armazenamento standard:** Uma conta de armazenamento Standard tem uma taxa máxima de pedido total de 20.000 IOPS. O IOPS total em todos os discos de máquinas virtuais numa conta de armazenamento Standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos altamente utilizados suportados por uma única conta de armazenamento Standard com base no limite da taxa de pedido. Por exemplo, para um VM de nível básico, o número máximo de discos altamente utilizados é de cerca de 66, que é de 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para um VM standard é de cerca de 40, que é de 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa total de produção máxima de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

