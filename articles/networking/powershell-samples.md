---
title: Amostras Azure PowerShell - Networking
description: Exemplos do Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: ca6ac145db0536d3cf7e5bcc72a58d72101ab12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459122"
---
# <a name="azure-powershell-samples-for-networking"></a>Amostras Azure PowerShell para networking

A tabela seguinte inclui links para scripts construídos com o Azure PowerShell.

| | |
|-|-|
|**Conectividade entre os recursos azure**||
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP, enquanto o tráfego para a sub-rede de back-end está limitado a SQL, porta 1433. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a subnet frontal está limitado a HTTP e HTTPS.. O tráfego de saída para a Internet a partir da subnet de back-end não é permitido. |
|**Balanceamento de carga e direção de tráfego**||
| [Tráfego de equilíbrio de carga para VMs para alta disponibilidade](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada em carga. |
| [Tráfego direto em várias regiões para alta disponibilidade de aplicações](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria dois planos de serviço de aplicações, duas aplicações web, um perfil de gestor de tráfego e dois pontos finais do gestor de tráfego. |
| | |
