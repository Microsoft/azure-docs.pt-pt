---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184212"
---
É importante perceber que existem duas formas de configurar um ouvinte de grupo de disponibilidade em Azure. As formas diferem no tipo de equilíbrio de carga Azure que utiliza quando cria o ouvinte. A tabela que se segue descreve as diferenças:

| Tipo de balanceador de carga | Implementação | Utilize se: |
| --- | --- | --- |
| **Externo** |Utiliza o *endereço IP virtual público* do serviço de nuvem que acolhe as máquinas virtuais (VMs). |Precisa de aceder ao ouvinte de fora da rede virtual, incluindo a partir da Internet. |
| **Interno** |Usa um *equilibrista de carga interna* com um endereço privado para o ouvinte. |Só pode aceder ao ouvinte a partir da mesma rede virtual. Este acesso inclui VPN site-to-site em cenários híbridos. |

> [!IMPORTANT]
> Para um ouvinte que utiliza o VIP público do serviço na nuvem (equilibrador de carga externa), desde que o cliente, ouvinte e bases de dados estejam na mesma região de Azure, não incorrerá em taxas de egress. Caso contrário, quaisquer dados devolvidos através do ouvinte são considerados descontração, e são cobrados a taxas normais de transferência de dados. 
> 
> 

Um ILB só pode ser configurado em redes virtuais com âmbito regional. As redes virtuais existentes que foram configuradas para um grupo de afinidade não podem usar um ILB. Para mais informações, consulte a [visão geral do equilibrador](../articles/load-balancer/load-balancer-internal-overview.md)de carga interna .

