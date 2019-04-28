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
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107998"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

Endereço IP 168.63.129.16 é um endereço IP público virtual que será utilizado para facilitar um canal de comunicação para recursos de plataforma do Azure. Os clientes podem definir qualquer espaço de endereços para a sua rede privada virtual no Azure. Por conseguinte, os recursos de plataforma do Azure tem de ser apresentados como um endereço IP público exclusivo. Este endereço IP virtual público facilita os seguintes procedimentos:

- Permite que o agente da VM comunicar com a plataforma do Azure para sinalizar que ele está num estado "Pronto".
- Permite a comunicação com o servidor virtual de DNS para fornecer a resolução de nomes filtrada para os recursos (como VM) que não têm um servidor DNS personalizado. Esta filtragem certifica-se de que os clientes podem resolver apenas os nomes de anfitrião de seus recursos.
- Permite que as sondas de estado de funcionamento do Balanceador de carga para determinar o estado de funcionamento das VMs num conjunto com balanceamento de carga.
- Permite que as mensagens de heartbeat de agente convidado para a função de PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Âmbito do endereço IP 168.63.129.16

Endereço IP público 168.63.129.16 virtual é utilizado em todas as regiões e todas as clouds nacionais. Este endereço IP público especial não será alterado. É permitido pela regra do grupo de segurança de rede padrão. Recomendamos que permite que este endereço IP em todas as políticas de local firewall. A comunicação entre este endereço IP especial e os recursos é segura porque apenas a plataforma do Azure interna pode obter uma mensagem a partir deste endereço IP. Se este endereço é bloqueado, pode ocorrer um comportamento inesperado numa variedade de cenários.

Além disso, pode esperar que o tráfego para o fluxo a partir do endereço IP público 168.63.129.16 virtual para o ponto final que está configurado para um [sonda de estado de funcionamento do Balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Num cenário de rede não virtuais, a sonda de estado de funcionamento é obtida a partir de um IP privado. 

## <a name="next-steps"></a>Passos Seguintes

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou eliminar um grupo de segurança de rede](manage-network-security-group.md)
