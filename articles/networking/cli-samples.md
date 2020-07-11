---
title: Amostras Azure CLI - Networking
description: Exemplos da CLI do Azure
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 397f2999821e97a1f12362e2aa2fa20b20e78596
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232411"
---
# <a name="azure-cli-samples-for-networking"></a>Amostras Azure CLI para networking

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| Script | Descrição |
|-|-|
|**Conectividade entre recursos Azure**||
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada na sub-rede frontal está limitado a HTTP, HTTPS e SSH. Não é permitido o tráfego de saída para a Internet a partir da sub-rede back-end. |
|**Equilíbrio de carga e direção de tráfego**||
| [Balancei vários websites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria dois VMs com múltiplas configurações IP, unidos a um Conjunto de Disponibilidade Azure, acessível através de um Balançador de Carga Azure. |
| [Tráfego direto em várias regiões para elevada disponibilidade de aplicações](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria dois planos de serviço de aplicações, duas aplicações web, um perfil de gestor de tráfego e dois pontos finais de gestor de tráfego. |
| | |
