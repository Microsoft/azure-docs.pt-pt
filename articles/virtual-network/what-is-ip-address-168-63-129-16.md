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
ms.openlocfilehash: 03c1badf984fb150631c157f3fdc07856b60e965
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088902"
---
# <a name="what-is-ip-address-1686312916"></a>O que é endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual que é usado para facilitar um canal de comunicação aos recursos da plataforma Azure. Os clientes podem definir qualquer espaço de endereço para a sua rede virtual privada em Azure. Por isso, os recursos da plataforma Azure devem ser apresentados como um endereço IP público único. Este endereço IP público virtual facilita as seguintes coisas:

- Permite ao Agente VM comunicar com a plataforma Azure para sinalizar que está em estado "Pronto".
- Permite a comunicação com o servidor virtual DNS fornecer resolução de nome filtrado aos recursos (como VM) que não têm um servidor DNS personalizado. Esta filtragem garante que os clientes podem resolver apenas os hostnames dos seus recursos.
- Permite que [as sondas de saúde do equilibrador de carga Azure](../load-balancer/load-balancer-custom-probe-overview.md) determinem o estado de saúde dos VM.
- Permite ao VM obter um endereço IP dinâmico a partir do serviço DHCP em Azure.
- Ativa as mensagens de batimento cardíaco do Agente Convidado para o papel paaS.

> [!NOTE]
> Num cenário de rede não virtual (Clássico), é utilizado um endereço IP privado em vez de 168.63.129.16. Este endereço IP privado é descoberto dinamicamente através do DHCP. As regras de firewall específicas para 168.63.129.16 devem ser ajustadas conforme apropriado.

## <a name="scope-of-ip-address-1686312916"></a>Âmbito de endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é utilizado em todas as regiões e nuvens nacionais. Este endereço IP público especial é propriedade da Microsoft e não será alterado. Recomendamos que permita este endereço IP em quaisquer políticas locais (na VM) de firewall (direção de saída). A comunicação entre este endereço IP especial e os recursos é segura porque apenas a plataforma Azure interna pode obter uma mensagem a partir deste endereço IP. Se este endereço estiver bloqueado, comportamentos inesperados podem ocorrer em vários cenários. 168.63.129.16 é um [IP virtual do nó de anfitrião](../virtual-network/security-overview.md#azure-platform-considerations) e, como tal, não está sujeito a rotas definidas pelo utilizador.

- O Agente VM requer comunicação de saída sobre as portas 80/tcp e 32526/tcp com o WireServer (168.63.129.16). Estes devem estar abertos na firewall local na VM. A comunicação sobre estes portos com 168.63.129.16 não está sujeita aos grupos de segurança de rede configurados.

- 168.63.129.16 pode prestar serviços DNS ao VM. Se tal não for desejado, o tráfego de saída para 168.63.129.16 portos 53/udp e 53/tcp pode ser bloqueado na firewall local no VM.

  Por predefinição, a comunicação DNS não está sujeita aos grupos de segurança de rede configurados, a menos que seja especificamente direcionada para alavancar a etiqueta de serviço [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags) Para bloquear o tráfego de DNS para Azure DNS através do NSG, crie uma regra de saída para negar o tráfego para [AzurePlatformDNS,](../virtual-network/service-tags-overview.md#available-service-tags)e especifique "*" como "Destination port ranges" e "Any" como protocolo.

- Quando o VM faz parte de um pool de backend do balançador de carga, a comunicação da sonda de [saúde](../load-balancer/load-balancer-custom-probe-overview.md) deve ser permitida a partir de 168.63.129.16. A configuração padrão do grupo de segurança da rede tem uma regra que permite esta comunicação. Esta regra aproveita a etiqueta de serviço [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) Se desejar, este tráfego pode ser bloqueado configurando o grupo de segurança da rede, no entanto isso resultará em sondas que falham.

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
