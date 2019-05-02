---
title: Introdução ao salto seguinte no observador de rede do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral do observador de rede de capacidade de salto seguinte.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: eb613455ccd6b6630d0d75b5a8458f812bb36846
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730221"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Utilize o salto seguinte para diagnosticar problemas de encaminhamento de máquina virtual

Tráfego de uma máquina virtual (VM) é enviado para um destino com base nas rotas efetivas associadas com uma interface de rede (NIC). Salto seguinte obtém o tipo de próximo salto e o endereço IP de um pacote a partir de uma VM específica e a NIC. Saber o salto seguinte ajuda-o a determinar se o tráfego está a ser direcionado para o destino pretendido, ou se o tráfego não está a ser enviado. Uma configuração incorreta de rotas, em que o tráfego é direcionado para uma localização no local ou uma aplicação virtual, pode levar a problemas de conectividade. Também o salto seguinte devolve a tabela de rotas associada com o próximo salto. Se a rota é definida como uma rota definida pelo utilizador, esse caminho é devolvido. Caso contrário, próximo salto devolve **rotas de sistema**.

![Descrição geral do próximo salto](./media/network-watcher-next-hop-overview/figure1.png)

Seguem-se os saltos seguintes adequados que podem ser devolvidos pela capacidade de salto seguinte:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Nenhuma

Para saber mais sobre cada tipo de próximo salto, veja [descrição geral do encaminhamento](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

Para saber como utilizar o salto seguinte para diagnosticar problemas de encaminhamento de rede VM, veja VM diagnosticar problemas de encaminhamento de utilização de rede a [portal do Azure](diagnose-vm-network-routing-problem.md), [PowerShell](diagnose-vm-network-routing-problem-powershell.md), ou o [da CLI do Azure](diagnose-vm-network-routing-problem-cli.md).
