---
title: O que é endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16, especificamente que é usado para facilitar um canal de comunicação aos recursos da plataforma Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 0f0bfa693086a3a097df219132d696a1d04e6f56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87286041"
---
# <a name="what-is-ip-address-1686312916"></a>O que é endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual que é usado para facilitar um canal de comunicação aos recursos da plataforma Azure. Os clientes podem definir qualquer espaço de endereço para a sua rede virtual privada em Azure. Por isso, os recursos da plataforma Azure devem ser apresentados como um endereço IP público único. Este endereço IP público virtual facilita as seguintes coisas:

- Permite ao Agente VM comunicar com a plataforma Azure para sinalizar que está em estado "Pronto".
- Permite a comunicação com o servidor virtual DNS fornecer resolução de nome filtrado aos recursos (como VM) que não têm um servidor DNS personalizado. Esta filtragem garante que os clientes podem resolver apenas os hostnames dos seus recursos.
- Permite que [as sondas de saúde do equilibrador de carga Azure](../load-balancer/load-balancer-custom-probe-overview.md) determinem o estado de saúde dos VM.
- Permite ao VM obter um endereço IP dinâmico a partir do serviço DHCP em Azure.
- Ativa as mensagens de batimento cardíaco do Agente Convidado para o papel paaS.

## <a name="scope-of-ip-address-1686312916"></a>Âmbito de endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é utilizado em todas as regiões e nuvens nacionais. Este endereço IP público especial é propriedade da Microsoft e não será alterado. Recomendamos que permita este endereço IP em quaisquer políticas locais (na VM) de firewall (direção de saída). A comunicação entre este endereço IP especial e os recursos é segura porque apenas a plataforma Azure interna pode obter uma mensagem a partir deste endereço IP. Se este endereço estiver bloqueado, comportamentos inesperados podem ocorrer em vários cenários. 168.63.129.16 é um [IP virtual do nó de anfitrião](../virtual-network/security-overview.md#azure-platform-considerations) e, como tal, não está sujeito a rotas definidas pelo utilizador.

- O Agente VM requer comunicação de saída sobre as portas 80, 443, 32526 com WireServer (168.63.129.16). Estes devem estar abertos na firewall local na VM. A comunicação sobre estes portos com 168.63.129.16 não está sujeita aos grupos de segurança de rede configurados.
- 168.63.129.16 pode prestar serviços DNS ao VM. Se isso não for desejado, este tráfego pode ser bloqueado na firewall local no VM. Por predefinição, a comunicação DNS não está sujeita aos grupos de segurança de rede configurados, a menos que seja especificamente direcionada para alavancar a etiqueta de serviço [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags) Para bloquear o tráfego de DNS para Azure DNS através do NSG, crie uma regra de saída para negar o tráfego para [AzurePlatformDNS,](../virtual-network/service-tags-overview.md#available-service-tags)e especifique "*" como "Destination port ranges" e "Any" como protocolo.
- Quando o VM faz parte de um pool de backend do balançador de carga, a comunicação da sonda de [saúde](../load-balancer/load-balancer-custom-probe-overview.md) deve ser permitida a partir de 168.63.129.16. A configuração padrão do grupo de segurança da rede tem uma regra que permite esta comunicação. Esta regra aproveita a etiqueta de serviço [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) Se desejar, este tráfego pode ser bloqueado configurando o grupo de segurança da rede, no entanto isso resultará em sondas que falham.

Num cenário de rede não virtual (Classic), a sonda de saúde é proveniente de um IP privado e 168.63.129.16 não é utilizada.

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
