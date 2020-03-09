---
title: Co-localizar VMs Windows Azure
description: Saiba como a co-localização dos recursos da Azure VM pode melhorar a latência.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374818"
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