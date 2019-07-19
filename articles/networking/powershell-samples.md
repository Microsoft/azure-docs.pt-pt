---
title: Exemplos do Azure PowerShell | Microsoft Docs
description: Exemplos do Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: 38ee43b805f39e1a702033a53eb7bbc13c43aab6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875500"
---
# <a name="azure-powershell-samples-for-networking"></a>Exemplos de Azure PowerShell para rede

A tabela a seguir inclui links para scripts criados usando Azure PowerShell.

| | |
|-|-|
|**Conectividade entre os recursos do Azure**||
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP, enquanto o tráfego para a sub-rede de back-end está limitado a SQL, porta 1433. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP e HTTPS. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|**Balanceamento de carga e direção do tráfego**||
| [Balancear carga de tráfego para VMs de elevada disponibilidade](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Vários Web sites em VMs com balanceamento de carga](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria duas VMs com várias configurações de IP, associadas ao Azure conjunto de disponibilidade, acessível através de um balanceador de carga do Azure. |
| [Direcionar o tráfego entre várias regiões para alta disponibilidade de aplicativos](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria dois planos do serviço de aplicativo, dois aplicativos Web, um perfil do Gerenciador de tráfego e dois pontos de extremidade do Gerenciador de tráfego. |
| | |
