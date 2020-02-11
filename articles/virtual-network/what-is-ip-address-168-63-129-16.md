---
title: O que é endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16 e como funciona com os seus recursos.
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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114355"
---
# <a name="what-is-ip-address-1686312916"></a>O que é endereço IP 168.63.129.16?

Endereço IP 168.63.129.16 é um endereço IP público virtual que é usado para facilitar um canal de comunicação aos recursos da plataforma Azure. Os clientes podem definir qualquer espaço de endereço para a sua rede virtual privada em Azure. Por conseguinte, os recursos da plataforma Azure devem ser apresentados como um endereço IP público único. Este endereço IP público virtual facilita as seguintes coisas:

- Permite ao Agente VM comunicar com a plataforma Azure para sinalizar que se encontra num estado "Pronto".
- Permite que a comunicação com o servidor virtual DNS forneça uma resolução de nome filtrada aos recursos (como VM) que não possuam um servidor DNS personalizado. Esta filtragem garante que os clientes podem resolver apenas os nomes de anfitriões dos seus recursos.
- Permite que [as sondas de saúde do equilíbrio de carga Azure](../load-balancer/load-balancer-custom-probe-overview.md) determinem o estado de saúde dos VMs.
- Permite ao VM obter um endereço IP dinâmico do serviço DHCP em Azure.
- Permite mensagens de batimentocardíaco do Agente Convidado para o papel paaS.

## <a name="scope-of-ip-address-1686312916"></a>Âmbito do endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é utilizado em todas as regiões e em todas as nuvens nacionais. Este endereço IP público especial é propriedade da Microsoft e não vai mudar. Recomendamos que permita este endereço IP em quaisquer políticas locais (no VM) de firewall (direção de saída). A comunicação entre este endereço IP especial e os recursos é segura porque apenas a plataforma interna Azure pode obter uma mensagem deste endereço IP. Se este endereço estiver bloqueado, um comportamento inesperado pode ocorrer em vários cenários. 168.63.129.16 é um [IP virtual do nó hospedeiro](../virtual-network/security-overview.md#azure-platform-considerations) e, como tal, não está sujeito a rotas definidas pelo utilizador.

- O Agente VM requer comunicação de saída sobre as portas 80, 443, 32526 com o WireServer (168.63.129.16). Estes devem estar abertos na firewall local na VM. A comunicação sobre estes portos com 168.63.129.16 não está sujeita aos grupos de segurança da rede configurados.
- 168.63.129.16 pode prestar serviços de DNS ao VM. Se isso não for desejado, este tráfego pode ser bloqueado na firewall local no VM. Por padrão, a comunicação DNS não está sujeita aos grupos de segurança de rede configurados, a menos que seja especificamente direcionado para alavancar a etiqueta de serviço [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags)
- Quando o VM faz parte de um conjunto de backend de um equilibrista de carga, a comunicação da sonda de [saúde](../load-balancer/load-balancer-custom-probe-overview.md) deve ser autorizada a ter origem em 168.63.129.16. A configuração padrão do grupo de segurança da rede tem uma regra que permite esta comunicação. Esta regra alavanca a etiqueta de serviço [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) Se desejar, este tráfego pode ser bloqueado configurando o grupo de segurança da rede, no entanto, isso resultará em sondas que falham.

Num cenário de rede não virtual (Clássico), a sonda de saúde é proveniente de um IP privado e não é utilizada 168.63.129.16.

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
