---
title: O que é o endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16 e como ele funciona com os seus recursos.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833126"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

Endereço IP 168.63.129.16 é um endereço IP público virtual que será utilizado para facilitar um canal de comunicação para recursos de plataforma do Azure. Os clientes podem definir qualquer espaço de endereços para a sua rede privada virtual no Azure. Por conseguinte, os recursos de plataforma do Azure tem de ser apresentados como um endereço IP público exclusivo. Este endereço IP virtual público facilita os seguintes procedimentos:

- Permite que o agente da VM comunicar com a plataforma do Azure para sinalizar que ele está num estado "Pronto".
- Permite a comunicação com o servidor virtual de DNS para fornecer a resolução de nomes filtrada para os recursos (como VM) que não têm um servidor DNS personalizado. Esta filtragem certifica-se de que os clientes podem resolver apenas os nomes de anfitrião de seus recursos.
- Permite [sondas de estado de funcionamento do Balanceador de carga do Azure](../load-balancer/load-balancer-custom-probe-overview.md) para determinar o estado de funcionamento das VMs.
- Permite que a VM obter um endereço IP dinâmico do serviço DHCP no Azure.
- Permite que as mensagens de heartbeat de agente convidado para a função de PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Âmbito do endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é utilizado em todas as regiões e todas as clouds nacionais. Este endereço IP público especial pertence à Microsoft e não será alterado. É permitido pela regra do grupo de segurança de rede padrão. Recomendamos que permite que este endereço IP em todas as políticas de local firewall. A comunicação entre este endereço IP especial e os recursos é segura porque apenas a plataforma do Azure interna pode obter uma mensagem a partir deste endereço IP. Se este endereço é bloqueado, pode ocorrer um comportamento inesperado numa variedade de cenários.

[Sondas de estado de funcionamento do Balanceador de carga do Azure](../load-balancer/load-balancer-custom-probe-overview.md) são originados por este endereço IP. Se bloquear este endereço IP, sua sondas irão falhar.

Num cenário de rede não virtuais (clássico), a sonda de estado de funcionamento é obtida a partir de um IP privado e não é utilizado o 168.63.129.16.

## <a name="next-steps"></a>Passos Seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
