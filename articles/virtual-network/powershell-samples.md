---
title: Exemplos do Azure PowerShell para a rede virtual
description: Exemplos do Azure PowerShell para a rede virtual.
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
ms.openlocfilehash: de752cdacf17193d5be95b2b9f887938ace2d50f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091875"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Exemplos do Azure PowerShell para a rede virtual

A tabela seguinte inclui ligações para scripts do Azure PowerShell:

| | |
|----|----|
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP, enquanto o tráfego para a sub-rede de back-end está limitado a SQL, porta 1433. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP e HTTPS. Não é permitido o tráfego de saída à Internet a partir da sub-rede de back-end. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Load Balancer básica](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Implanta a rede virtual de pilha dupla (IPv4 + IPv6) com duas VMs e um Load Balancer básico do Azure com endereços IP públicos IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Implanta a rede virtual de pilha dupla (IPv4 + IPv6) com duas VMs e uma Standard Load Balancer do Azure com endereços IP públicos IPv4 e IPv6. |
