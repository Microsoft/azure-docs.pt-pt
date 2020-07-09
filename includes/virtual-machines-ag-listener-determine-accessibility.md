---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184212"
---
É importante perceber que há duas formas de configurar um ouvinte de grupo de disponibilidade em Azure. As formas diferem no tipo de balançador de carga Azure que utiliza quando cria o ouvinte. A tabela a seguir descreve as diferenças:

| Tipo de balançador de carga | Implementação | Utilize se: |
| --- | --- | --- |
| **Externo** |Utiliza o *endereço IP virtual público* do serviço de nuvem que acolhe as máquinas virtuais (VMs). |Tem de aceder ao ouvinte de fora da rede virtual, incluindo a partir da Internet. |
| **Interno** |Utiliza um *equilibrador de carga interno* com um endereço privado para o ouvinte. |Só pode aceder ao ouvinte dentro da mesma rede virtual. Este acesso inclui VPN site-to-site em cenários híbridos. |

> [!IMPORTANT]
> Para um ouvinte que utilize o VIP público do serviço de nuvem (equilibrador de carga externo), desde que o cliente, ouvinte e bases de dados estejam na mesma região de Azure, não irá incorrer em encargos de erupção. Caso contrário, qualquer dado devolvido através do ouvinte é considerado saída, e é cobrado a taxas normais de transferência de dados. 
> 
> 

Um ILB só pode ser configurado em redes virtuais com âmbito regional. As redes virtuais existentes que foram configuradas para um grupo de afinidade não podem utilizar um ILB. Para obter mais informações, consulte [a visão geral do balançador de carga interna](../articles/load-balancer/load-balancer-internal-overview.md).

