---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097723"
---
É importante perceber que existem duas formas de configurar um serviço de escuta do grupo de disponibilidade no Azure. As formas são diferentes no tipo de Balanceador de carga do Azure que utiliza quando cria o serviço de escuta. A tabela seguinte descreve as diferenças:

| Tipo de Balanceador de carga | Implementação | Utilize se: |
| --- | --- | --- |
| **Externo** |Utiliza a *endereço IP virtual público* do serviço em nuvem que aloja as máquinas virtuais (VMs). |Precisa acessar o serviço de escuta de fora da rede virtual, incluindo a partir da Internet. |
| **Interno** |Utiliza um *Balanceador de carga interno* com um endereço privado para o serviço de escuta. |Pode acessar o serviço de escuta apenas a partir de dentro da mesma rede virtual. Este acesso inclui VPN site a site em cenários híbridos. |

> [!IMPORTANT]
> Para um serviço de escuta que utiliza VIP público o serviço em nuvem (Balanceador de carga externo), contanto que o cliente, serviço de escuta e bases de dados estão na mesma região do Azure, não incorrerá em custos de saída. Caso contrário, todos os dados devolvidos por meio do serviço de escuta são considerados a saída, e ele é cobrado às tarifas de transferência de dados normal. 
> 
> 

Pode ser configurado um ILB apenas em redes virtuais com um âmbito regional. Redes virtuais existentes que foram configuradas para um grupo de afinidade não podem utilizar um ILB. Para obter mais informações, consulte [descrição geral do Balanceador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

<!-- Update_Description: update meta properties -->