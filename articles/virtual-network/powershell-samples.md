---
title: Exemplos do Azure PowerShell para a rede virtual
description: Saiba mais sobre as amostras do Azure PowerShell para gerir redes virtuais, incluindo uma amostra para criar uma rede virtual para aplicações multi-camadas.
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
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288229"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Exemplos do Azure PowerShell para a rede virtual

A tabela seguinte inclui ligações para scripts do Azure PowerShell:

| Script | Description |
|----|----|
| [Criar uma rede virtual para aplicações de várias camadas](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego para a sub-rede do front-end está limitado a HTTP, enquanto o tráfego para a sub-rede de back-end está limitado a SQL, porta 1433. |
| [Peering de rede de duas redes virtuais](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Cria e liga duas redes virtuais na mesma região. |
| [Encaminhar o tráfego através de uma aplicação virtual de rede](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Cria uma rede virtual com sub-redes de front-end e back-end e uma VM com capacidade para encaminhar o tráfego entre as duas sub-redes. |
| [Filtrar o tráfego de rede VM de entrada e saída](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP e HTTPS. Não é permitido o tráfego de saída à Internet a partir da sub-rede de back-end. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Balancer de Carga Básica](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Implementa rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Balançador de Carga Básica Azure com endereços IP públicos IPv4 e IPv6. |
|[Configurar rede virtual de pilha dupla IPv4 + IPv6 com Balanceador de Carga Padrão](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Implementa rede virtual de dupla pilha (IPv4+IPv6) com dois VMs e um Equilibr de Carga Padrão Azure com endereços IP públicos IPv4 e IPv6. |
