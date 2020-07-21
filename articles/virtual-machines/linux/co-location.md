---
title: Co-localizar Os VMs do Linux
description: Saiba como a co-localização dos recursos Azure VM pode melhorar a latência.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 0a28b238701eeb0572149fb55e1633cdd40ec0a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511029"
---
# <a name="co-locate-resources-for-improved-latency"></a>Co-localizar recursos para uma maior latência

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 

## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximos passos

Utilize um VM para um [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando o Azure CLI.

Saiba como testar a [latência da rede.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Saiba como otimizar o [rendimento da rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
