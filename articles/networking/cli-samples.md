---
title: Exemplos de CLI do Azure – rede
description: Exemplos da CLI do Azure
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: ae8ea3db10f1a40fe4d3bbca94db61d425b6092d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091469"
---
# <a name="azure-cli-samples-for-networking"></a>Exemplos de CLI do Azure para rede

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|-|-|
|**Conectividade entre os recursos do Azure**||
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP, HTTPS e SSH. O tráfego de saída para a Internet da sub-rede de back-end não é permitido. |
|**Balanceamento de carga e direção do tráfego**||
| [Balancear carga de tráfego para VMs de elevada disponibilidade](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Vários Web sites em VMs com balanceamento de carga](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Cria duas VMs com várias configurações de IP, associadas ao Azure conjunto de disponibilidade, acessível através de um balanceador de carga do Azure. |
| [Direcionar o tráfego entre várias regiões para alta disponibilidade de aplicativos](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Cria dois planos do serviço de aplicativo, dois aplicativos Web, um perfil do Gerenciador de tráfego e dois pontos de extremidade do Gerenciador de tráfego. |
| | |
