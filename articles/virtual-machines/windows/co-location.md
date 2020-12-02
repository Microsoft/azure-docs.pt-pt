---
title: Co-localizar VMs para uma maior latência
description: Saiba como a co-localização dos recursos Azure VM pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: eb7b5d3b477a511432dbd3ee8a6b382002aaab44
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488476"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para uma maior latência

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 


## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Implementar um VM num [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando a Azure PowerShell.

Saiba como testar a [latência da rede.](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Saiba como otimizar o [rendimento da rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).