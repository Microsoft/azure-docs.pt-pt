---
title: Introdução ao próximo salto no Azure Network Watcher Microsoft Docs
description: Este artigo fornece uma visão geral da próxima capacidade de lúpulo do Observador de Rede.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76844063"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Use o próximo salto para diagnosticar problemas de encaminhamento de máquinas virtuais

O tráfego de uma máquina virtual (VM) é enviado para um destino baseado nas rotas eficazes associadas a uma interface de rede (NIC). Em seguida, o hop obtém o próximo tipo de lúpulo e endereço IP de um pacote de um VM e NIC específicos. Saber o próximo salto ajuda-o a determinar se o tráfego está a ser direcionado para o destino pretendido, ou se o tráfego não está a ser enviado para lado nenhum. Uma configuração inadequada das rotas, onde o tráfego é direcionado para um local no local, ou um aparelho virtual, pode levar a problemas de conectividade. O próximo salto também devolve a tabela de rotas associada ao próximo salto. Se a rota for definida como uma rota definida pelo utilizador, essa rota é devolvida. Caso contrário, o próximo lúpulo devolve **a Rota do Sistema**.

![próxima visão geral do salto](./media/network-watcher-next-hop-overview/figure1.png)

Os próximos saltos que podem ser devolvidos pela próxima capacidade de lúpulo são os seguintes:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Nenhum

Para saber mais sobre cada próximo tipo de lúpulo, consulte [a visão geral do Encaminhamento](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes

Para aprender a utilizar o próximo salto para diagnosticar problemas de encaminhamento de rede VM, consulte os problemas de encaminhamento da rede VM do diagnóstico utilizando o [portal Azure](diagnose-vm-network-routing-problem.md), [PowerShell](diagnose-vm-network-routing-problem-powershell.md), ou o [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
