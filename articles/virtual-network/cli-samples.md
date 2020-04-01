---
title: Exemplos da CLI do Azure para a rede virtual
description: Exemplos da CLI do Azure para a rede virtual.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271209"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Exemplos da CLI do Azure para a rede virtual

A tabela seguinte inclui ligações para scripts bash com comandos da CLI do Azure:

| | |
|----|----|
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP, HTTPS e SSH. Não é permitido o tráfego de saída à Internet a partir da sub-rede de back-end. |
|[Configure IPv4 + IPv6 dual stack rede virtual com Equilíbrio de Carga Básico](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Implementa uma rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Equilíbrio de Carga Básico Azure com endereços IP públicos IPv4 e IPv6. |
|[Configure IPv4 + IPv6 dual stack rede virtual com Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Implementa uma rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Equilíbrio de Carga Padrão Azure com endereços IP públicos IPv4 e IPv6. |
|[Tutorial: Criar e testar um portal NAT - Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Crie e valide um portal NAT utilizando uma máquina virtual de origem e destino. |