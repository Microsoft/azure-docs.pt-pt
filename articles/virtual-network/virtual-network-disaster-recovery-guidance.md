---
title: '| de continuidade de negócios de rede virtual Microsoft Docs'
description: Saiba o que fazer em caso de uma rutura do serviço Azure com impacto nas Redes Virtuais Azure.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67876178"
---
# <a name="virtual-network--business-continuity"></a>Rede Virtual – Continuidade empresarial

## <a name="overview"></a>Descrição Geral
Uma Rede Virtual (VNet) é uma representação lógica da sua rede na nuvem. Permite-lhe definir o seu próprio espaço de endereço IP privado e segmentar a rede em sub-redes. Os VNets servem como um limite fiduciário para hospedar os seus recursos de computação, tais como Azure Virtual Machines e Cloud Services (funções web/trabalhador). Um VNet permite a comunicação IP privada direta entre os recursos nele alojados. Pode ligar uma rede virtual a uma rede no local através de um Gateway VPN ou ExpressRoute.

Um VNet é criado no âmbito de uma região. Pode *criar* VNets com o mesmo espaço de endereço em duas regiões diferentes (por exemplo, EUA Leste e EUA West), mas como eles têm o mesmo espaço de endereço, você não pode conectá-los juntos. 

## <a name="business-continuity"></a>Continuidade do Negócio

Pode haver várias maneiras diferentes de a sua aplicação ser interrompida. Uma região poderia ser completamente isolada devido a um desastre natural, ou a um desastre parcial, devido a uma falha de vários dispositivos ou serviços. O impacto no serviço VNet é diferente em cada uma destas situações.

**P: Se ocorrer uma paralisação para toda uma região, o que faço? Por exemplo, se uma região está completamente isolada devido a uma catástrofe natural? O que acontece com as redes virtuais hospedadas na região?**

R: A rede virtual e os recursos na região afetada permanecem inacessíveis durante o período de interrupção do serviço.

![Diagrama de rede virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso fazer para recriar a mesma rede virtual numa região diferente?**

R: As redes virtuais são recursos bastante leves. Pode invocar apis Azure para criar um VNet com o mesmo espaço de endereço numa região diferente. Para recriar o mesmo ambiente que estava presente na região afetada, faz chamadas de API para recolocar os papéis web e trabalhadores dos Cloud Services e das máquinas virtuais que tinha. Se tiver conectividade no local, como numa implementação híbrida, tem de implementar um novo Gateway VPN e ligar-se à sua rede no local.

Para criar uma rede virtual, consulte [criar uma rede virtual.](manage-virtual-network.md#create-a-virtual-network)

**P: Pode uma réplica de um VNet numa determinada região ser recriada noutra região antes do tempo?**

R: Sim, você pode criar dois VNets usando o mesmo espaço de endereço IP privado e recursos em duas regiões diferentes antes do tempo. Se você está hospedando serviços virados para a Internet no VNet, você poderia ter configurado Gestor de Tráfego para geo-rota tráfego para a região que está ativa. No entanto, não é possível ligar dois VNets com o mesmo espaço de endereço à sua rede no local, uma vez que provocaria problemas de encaminhamento. No momento de um desastre e perda de um VNet numa região, você pode ligar o outro VNet na região disponível, com o espaço de endereço correspondente à sua rede no local.

Para criar uma rede virtual, consulte [criar uma rede virtual.](manage-virtual-network.md#create-a-virtual-network)

