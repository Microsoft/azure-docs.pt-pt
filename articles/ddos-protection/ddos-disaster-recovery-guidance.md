---
title: Azure DDoS Protection A continuidade de negócios standard | Microsoft Docs
description: Saiba o que fazer em caso de perturbação do serviço Azure com impacto no Padrão de Proteção Azure DDoS.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730499"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – continuidade do negócio

A continuidade do negócio e a recuperação de desastres na Azure DDoS Protection Standard permite que o seu negócio continue a operar face a uma perturbação. Este artigo discute disponibilidade (intrarregião) e recuperação de desastres.

## <a name="overview"></a>Descrição Geral
O Azure DDoS Protection Standard protege os endereços IP públicos nas redes virtuais. A proteção é simples de permitir qualquer rede virtual nova ou existente e não requer qualquer aplicação ou alteração de recursos.

Uma Rede Virtual (VNet) é uma representação lógica da sua rede na nuvem. Os VNets servem como um limite fiduciário para hospedar os seus recursos, tais como Azure Application Gateway, Azure Firewall e Azure Virtual Machines. É criado no âmbito de uma região. Pode *criar* VNets com o mesmo espaço de endereço em duas regiões diferentes (por exemplo, EUA Leste e EUA West), mas como eles têm o mesmo espaço de endereço, você não pode conectá-los juntos. 

## <a name="business-continuity"></a>Continuidade do negócio

Pode haver várias maneiras diferentes de a sua aplicação ser interrompida. Uma região poderia ser completamente isolada devido a um desastre natural, ou a um desastre parcial, devido a uma falha de vários dispositivos ou serviços. O impacto nos seus VNets protegidos é diferente em cada uma destas situações.

**P: Se ocorrer uma paralisação para toda uma região, o que faço? Por exemplo, se uma região está completamente isolada devido a uma catástrofe natural? O que acontece com as redes virtuais hospedadas na região?**

R: A rede virtual e os recursos na região afetada permanecem inacessíveis durante o período de interrupção do serviço.

![Diagrama de rede virtual simples.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso fazer para recriar a mesma rede virtual numa região diferente?**

R: As redes virtuais são recursos bastante leves. Pode invocar apis Azure para criar um VNet com o mesmo espaço de endereço numa região diferente. Para recriar o mesmo ambiente que estava presente na região afetada, faz chamadas da API para redistribuir os recursos nos VNets que tinha. Se tiver conectividade no local, como numa implementação híbrida, tem de implementar um novo Gateway VPN e ligar-se à sua rede no local.

Para criar uma rede virtual, consulte [criar uma rede virtual.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

**P: Pode uma réplica de um VNet numa determinada região ser recriada noutra região antes do tempo?**

R: Sim, você pode criar dois VNets usando o mesmo espaço de endereço IP privado e recursos em duas regiões diferentes antes do tempo. Se você está hospedando serviços virados para a Internet no VNet, você poderia ter configurado Gestor de Tráfego para geo-rota tráfego para a região que está ativa. No entanto, não é possível ligar dois VNets com o mesmo espaço de endereço à sua rede no local, uma vez que provocaria problemas de encaminhamento. No momento de um desastre e perda de um VNet numa região, você pode ligar o outro VNet na região disponível, com o espaço de endereço correspondente à sua rede no local.

Para criar uma rede virtual, consulte [criar uma rede virtual.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um plano de proteção DDoS](manage-ddos-protection.md).