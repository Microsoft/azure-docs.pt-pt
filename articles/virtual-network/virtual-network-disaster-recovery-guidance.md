---
title: Continuidade do negócio de rede virtual | Documentos da Microsoft
description: Saiba o que fazer em caso de interrupção de serviço do Azure a afetar a redes virtuais do Azure.
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
ms.author: narayan;aglick
ms.openlocfilehash: f8068524a33c6d29056af456a26b8bd87fc3e3d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742716"
---
# <a name="virtual-network--business-continuity"></a>Rede virtual – continuidade do negócio

## <a name="overview"></a>Descrição geral
Uma rede Virtual (VNet) é uma representação lógica da sua rede na cloud. Permite-lhe definir seu próprio espaço de endereços IP privados e segmentar a rede em sub-redes. VNets serve como um limite de fidedignidade para alojar os seus recursos de computação, como máquinas virtuais do Azure e serviços Cloud (funções da web/trabalho). Uma VNet permite a comunicação de IP privada direta entre os recursos alojados no mesmo. Pode ligar uma rede virtual a uma rede no local através de um Gateway de VPN ou ExpressRoute.

Uma VNet é criada dentro do escopo de uma região. Pode criar VNets com o mesmo espaço de endereços em duas regiões diferentes (por exemplo, EUA leste e E.u.a. oeste), mas não é possível ligá-los. 

## <a name="business-continuity"></a>Continuidade do Negócio

Pode haver várias formas diferentes de que seu aplicativo pode ser interrompido. Uma região poderia ser cortada completamente devido a um desastre natural ou um desastre parcial, devido a uma falha de vários dispositivos ou serviços. O impacto sobre o serviço de VNet é diferente em cada uma destas situações.

**P: Se ocorrer uma falha para uma região inteira, o que devo fazer? Por exemplo, se uma região é cortada completamente devido a um desastre natural? O que acontece com as redes virtuais alojadas na região?**

R: A rede virtual e os recursos na região afetado permanece inacessível durante o tempo de interrupção do serviço.

![Diagrama de rede Virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso voltar a criar a mesma rede virtual numa região diferente?**

R: Redes virtuais são recursos bem leves. É possível invocar as APIs do Azure para criar uma VNet com o mesmo espaço de endereços numa região diferente. Para recriar o mesmo ambiente que estava presente na região afetado, fazer chamadas da API da web de serviços Cloud e funções de trabalho e as máquinas virtuais que tinha de voltar a. Se tiver conectividade no local, tal como numa implementação híbrida, terá de implementar um novo Gateway de VPN e ligar à sua rede no local.

Para criar uma rede virtual, veja [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network).

**P: Uma réplica de uma VNet numa determinada região pode ser recriada noutra região antes do tempo?**

R: Sim, pode criar duas VNets com o mesmo espaço de endereços IP privados e os recursos em duas regiões diferentes antes do tempo. Se estiver a alojar os serviços de acesso à internet na VNet, ter definido o Gestor de tráfego para geo-encaminhar o tráfego para a região que está ativa. No entanto, não pode ligar duas VNets com o mesmo espaço de endereço para a sua rede no local, como iria provocar problemas de encaminhamento. No momento de um desastre e perda de uma VNet na região, pode ligar a outra VNet na região disponível, com o espaço de endereços correspondente à sua rede no local.

Para criar uma rede virtual, veja [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network).

