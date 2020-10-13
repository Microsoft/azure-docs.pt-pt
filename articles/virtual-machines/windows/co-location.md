---
title: Co-localizar VMs para uma maior latência
description: Saiba como a co-localização dos recursos Azure VM pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 9c981e9b829fa10f094370e1e1ffd8b6df967d4b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965782"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para uma maior latência

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 


## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Implementar um VM num [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando a Azure PowerShell.

Saiba como testar a [latência da rede.](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)

Saiba como otimizar o [rendimento da rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).