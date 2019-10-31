---
title: Colocalizar VMs do Azure do Linux
description: Saiba como a colocalização de recursos de VM do Azure pode melhorar a latência.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: c90e70900c019340cacc169325155ac74cf7807c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171479"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalizar recursos para maior latência

Ao implantar seu aplicativo no Azure, a difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Implante uma VM em um [grupo de posicionamento de proximidade](proximity-placement-groups.md) usando o CLI do Azure.

Saiba como [testar a latência de rede](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Saiba como [otimizar a taxa de transferência de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Saiba como [usar grupos de posicionamento de proximidade com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
