---
title: Co-localizar VMs para uma latência melhorada
description: Saiba como a co-localização dos recursos da Azure VM pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083177"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para uma latência melhorada

Ao implementar a sua aplicação no Azure, a difusão de instâncias por regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 


## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Desloque um VM para um grupo de [colocação de proximidade](proximity-placement-groups.md) utilizando o Azure PowerShell.

Aprenda a testar a [latência da rede.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Aprenda a [otimizar](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)a entrada da rede .  

Saiba como utilizar grupos de [colocação de proximidade com aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).