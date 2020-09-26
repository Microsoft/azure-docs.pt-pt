---
title: Co-localizar Os VMs do Linux
description: Saiba como a co-localização dos recursos Azure VM para o Linux pode melhorar a latência.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 6bf01f4da6ce96b413c87b81c14a998603cebd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279728"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalizar recursos para latência melhorada

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 

## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Utilize um VM para um [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando o Azure CLI.

Saiba como testar a [latência da rede.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Saiba como otimizar o [rendimento da rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
