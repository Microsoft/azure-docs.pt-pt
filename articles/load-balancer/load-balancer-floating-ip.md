---
title: Configuração IP flutuante do balançador de carga Azure
description: Visão geral do Azure Load Balancer Floating IP
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699411"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Configuração IP flutuante do balançador de carga Azure

O balanceador de carga fornece várias capacidades tanto para aplicações UDP como para TCP.

## <a name="floating-ip"></a>IP Flutuante

Alguns cenários de aplicação preferem ou exigem que a mesma porta seja usada por múltiplas instâncias de aplicação num único VM na piscina de backend. Exemplos comuns de reutilização portuária incluem agrupamentos para alta disponibilidade, aparelhos virtuais de rede e exposição de vários pontos finais TLS sem reen encriptação. Se pretender reutilizar a porta de backend através de várias regras, deve ativar o IP flutuante na definição de regra.

**Ip flutuante** é a terminologia de Azure para uma parte do que é conhecido como Retorno do Servidor Direto (DSR). A DSR é constituída por duas partes:

- Topologia do fluxo
- Um esquema de mapeamento de endereços IP

A nível da plataforma, o Azure Load Balancer opera sempre numa topologia de fluxo DSR, independentemente de o IP flutuante estar ou não ativado. Isto significa que a parte de saída de um fluxo é sempre corretamente reescrita para fluir diretamente de volta à origem.
Sem IP flutuante, o Azure expõe um sistema tradicional de mapeamento de endereço IP de equilíbrio de carga para facilitar a utilização (o IP das instâncias VM). Ativar o IP flutuante altera o mapeamento do endereço IP para o Frontend IP do Balanceador de carga para permitir uma flexibilidade adicional. Sabia mais [aqui](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Limitações

- O IP flutuante não é atualmente suportado em configurações ip secundárias para cenários de equilíbrio de carga

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um balanceador de carga padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com um balanceador de carga.
- Saiba mais [sobre as ligações de saída do Azure Load Balancer](load-balancer-outbound-connections.md).
- Saiba mais sobre [o Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [as Sondas de Saúde.](load-balancer-custom-probe-overview.md)
- Saiba mais sobre [diagnósticos de balanceadores de carga padrão](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [grupos de segurança de rede.](../virtual-network/network-security-groups-overview.md)