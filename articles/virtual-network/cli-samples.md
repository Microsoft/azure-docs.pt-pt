---
title: Exemplos da CLI do Azure para a rede virtual
description: Saiba mais sobre vários scripts de amostras que pode usar para completar tarefas no CLI Azure, incluindo a criação de uma rede virtual para aplicações multi-níveis.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 97d63598f6a9cca199779e56cc211b9622117301
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048838"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Exemplos da CLI do Azure para a rede virtual

A tabela seguinte inclui ligações para scripts bash com comandos da CLI do Azure:

| Script | Description |
|----|----|
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP e SSH, enquanto o tráfego para a sub-rede de back-end está limitado a MySQL, porta 3306. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP, HTTPS e SSH. Não é permitido o tráfego de saída à Internet a partir da sub-rede de back-end. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Balancer de Carga Básica](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Implementa rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Balançador de Carga Básica Azure com endereços IP públicos IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Balanceador de Carga Padrão](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Implementa rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Equilibr de Carga Padrão Azure com endereços IP públicos IPv4 e IPv6. |
|[Tutorial: Criar e testar uma porta de entrada NAT - Azure CLI](./tutorial-create-nat-gateway-cli.md)|Crie e valide um gateway NAT utilizando uma máquina virtual de origem e destino. |