---
title: Co-localizar VMs para uma maior latência
description: Saiba como a co-localização dos recursos Azure VM pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82083177"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para uma maior latência

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 


## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Implementar um VM num [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando a Azure PowerShell.

Saiba como testar a [latência da rede.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Saiba como otimizar o [rendimento da rede.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).