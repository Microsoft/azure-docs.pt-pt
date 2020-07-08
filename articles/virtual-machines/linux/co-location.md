---
title: Co-localizar Os VMs do Linux
description: Saiba como a co-localização dos recursos Azure VM pode melhorar a latência.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79250286"
---
# <a name="co-locate-resources-for-improved-latency"></a>Co-localizar recursos para uma maior latência

Ao implementar a sua aplicação no Azure, espalhar instâncias através de regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho global da sua aplicação. 

## <a name="proximity-placement-groups"></a>Grupos de colocação de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximos passos

Utilize um VM para um [grupo de colocação de proximidade](proximity-placement-groups.md) utilizando o Azure CLI.

Saiba como testar a [latência da rede.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Saiba como otimizar o [rendimento da rede.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Saiba como [utilizar grupos de colocação de proximidade com aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
