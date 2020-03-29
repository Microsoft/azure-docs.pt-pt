---
title: Introdução ao próximo salto no Vigilante da Rede Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral da próxima capacidade de lúpulo do Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844063"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Use o próximo salto para diagnosticar problemas de encaminhamento de máquinas virtuais

O tráfego de uma máquina virtual (VM) é enviado para um destino com base nas rotas eficazes associadas a uma interface de rede (NIC). O próximo salto recebe o próximo tipo de lúpulo e endereço IP de um pacote de um VM e NIC específicos. Conhecer o próximo salto ajuda-o a determinar se o tráfego está a ser direcionado para o destino pretendido, ou se o tráfego não está a ser enviado para lado nenhum. Uma configuração inadequada das rotas, onde o tráfego é direcionado para um local no local, ou um aparelho virtual, pode levar a problemas de conectividade. O próximo salto também devolve a tabela de rotas associada ao próximo salto. Se a rota for definida como uma rota definida pelo utilizador, essa rota é devolvida. Caso contrário, o próximo salto retorna **a Rota do Sistema.**

![próxima visão geral do salto](./media/network-watcher-next-hop-overview/figure1.png)

Os próximos lúpulos que poderão ser devolvidos pela próxima capacidade de lúpulo são os seguintes:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Nenhuma

Para saber mais sobre cada tipo de lúpulo seguinte, consulte a [visão geral do Routing.](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar o próximo salto para diagnosticar problemas de encaminhamento de rede VM, consulte problemas de encaminhamento da rede VM diagnosticados utilizando o [portal Azure,](diagnose-vm-network-routing-problem.md) [PowerShell](diagnose-vm-network-routing-problem-powershell.md)ou o [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
