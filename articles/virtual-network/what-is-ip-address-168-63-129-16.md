---
title: O que é o endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16 e como ele funciona com seus recursos.
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
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067784"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual que é usado para facilitar um canal de comunicação para os recursos da plataforma Azure. Os clientes podem definir qualquer espaço de endereço para sua rede virtual privada no Azure. Portanto, os recursos da plataforma Azure devem ser apresentados como um endereço IP público exclusivo. Esse endereço IP público virtual facilita as seguintes coisas:

- Permite que o agente de VM se comunique com a plataforma do Azure para sinalizar que está em um estado "pronto".
- Permite a comunicação com o servidor virtual DNS para fornecer resolução de nomes filtrados para os recursos (como VM) que não têm um servidor DNS personalizado. Essa filtragem garante que os clientes possam resolver somente os nomes de host de seus recursos.
- Habilita [investigações de integridade do Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) para determinar o estado de integridade das VMs.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.
- Habilita mensagens de pulsação do agente convidado para a função PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Escopo do endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é usado em todas as regiões e em todas as nuvens nacionais. Esse endereço IP público especial pertence à Microsoft e não será alterado. Ele é permitido pela regra de grupo de segurança de rede padrão. Recomendamos que você permita esse endereço IP em qualquer política de firewall local nas direções de entrada e saída. A comunicação entre esse endereço IP especial e os recursos é segura porque apenas a plataforma interna do Azure pode originar uma mensagem desse endereço IP. Se esse endereço for bloqueado, o comportamento inesperado poderá ocorrer em uma variedade de cenários.

[Azure Load Balancer investigações de integridade](../load-balancer/load-balancer-custom-probe-overview.md) se originam desse endereço IP. Se você bloquear esse endereço IP, suas investigações falharão.

Em um cenário de rede não virtual (clássico), a investigação de integridade é originada de um IP privado e 168.63.129.16 não é usado.

## <a name="next-steps"></a>Passos Seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
