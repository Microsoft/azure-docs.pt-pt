---
title: Continuidade do negócio da rede virtual [ Microsoft Docs
description: Saiba o que fazer em caso de interrupção do serviço Azure com impacto nas Redes Virtuais Azure.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876178"
---
# <a name="virtual-network--business-continuity"></a>Rede Virtual – Continuidade empresarial

## <a name="overview"></a>Descrição geral
Uma Rede Virtual (VNet) é uma representação lógica da sua rede na nuvem. Permite-lhe definir o seu próprio espaço de endereço IP privado e segmentar a rede em subredes. A VNets serve como um limite de confiança para acolher os seus recursos computacionais, tais como Máquinas Virtuais Azure e Serviços de Nuvem (funções web/trabalhador). Um VNet permite uma comunicação IP privada direta entre os recursos alojados no mesmo. Pode ligar uma rede virtual a uma rede no local através de um VPN Gateway ou ExpressRoute.

Um VNet é criado no âmbito de uma região. Você pode *criar* VNets com o mesmo espaço de endereço em duas regiões diferentes (por exemplo, US East e US West), mas como eles têm o mesmo espaço de endereço, você não pode conectá-los. 

## <a name="business-continuity"></a>Continuidade do Negócio

Pode haver várias maneiras diferentes de a sua aplicação poder ser interrompida. Uma região pode ser completamente isolada devido a uma catástrofe natural, ou a um desastre parcial, devido a uma falha de vários dispositivos ou serviços. O impacto no serviço VNet é diferente em cada uma destas situações.

**P: Se ocorrer uma paralisação para toda uma região, o que faço? Por exemplo, se uma região está completamente isolada devido a um desastre natural? O que acontece às redes virtuais alojadas na região?**

R: A rede virtual e os recursos na região afetada permanecem inacessíveis durante o período de interrupção do serviço.

![Diagrama de rede virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso fazer para recriar a mesma rede virtual numa região diferente?**

R: As redes virtuais são recursos bastante leves. Pode invocar APIs Azure para criar um VNet com o mesmo espaço de endereço numa região diferente. Para recriar o mesmo ambiente que esteve presente na região afetada, faz chamadas aPi para reimplantar as funções web e dos trabalhadores da Cloud Services, e as máquinas virtuais que tinha. Se tiver conectividade no local, como numa implantação híbrida, tem de implementar um novo VPN Gateway e ligar-se à sua rede no local.

Para criar uma rede virtual, consulte [Criar uma rede virtual.](manage-virtual-network.md#create-a-virtual-network)

**P: Pode uma réplica de uma VNet numa determinada região ser recriada noutra região antes do tempo?**

R: Sim, você pode criar dois VNets usando o mesmo espaço de endereço IP privado e recursos em duas regiões diferentes antes do tempo. Se você está hospedando serviços virados para a Internet no VNet, você poderia ter configurado Traffic Manager para tráfego geo-rotato para a região que está ativa. No entanto, não é possível ligar dois VNets com o mesmo espaço de endereço à sua rede no local, uma vez que causaria problemas de encaminhamento. No momento de um desastre e perda de um VNet numa região, pode ligar o outro VNet na região disponível, com o espaço de endereço correspondente à sua rede no local.

Para criar uma rede virtual, consulte [Criar uma rede virtual.](manage-virtual-network.md#create-a-virtual-network)

