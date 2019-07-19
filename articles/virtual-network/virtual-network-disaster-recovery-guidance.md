---
title: Continuidade dos negócios de rede virtual | Microsoft Docs
description: Saiba o que fazer no caso de uma interrupção do serviço do Azure afetar as redes virtuais do Azure.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876178"
---
# <a name="virtual-network--business-continuity"></a>Rede virtual – continuidade dos negócios

## <a name="overview"></a>Descrição geral
Uma rede virtual (VNet) é uma representação lógica de sua rede na nuvem. Ele permite que você defina seu próprio espaço de endereço IP privado e segmente a rede em sub-redes. VNets serve como um limite de confiança para hospedar seus recursos de computação, como máquinas virtuais do Azure e serviços de nuvem (funções Web/de trabalho). Uma VNet permite a comunicação de IP privado direto entre os recursos hospedados nela. Você pode vincular uma rede virtual a uma rede local por meio de um gateway de VPN ou ExpressRoute.

Uma VNet é criada dentro do escopo de uma região. Você pode *criar* VNets com o mesmo espaço de endereço em duas regiões diferentes (por exemplo, leste dos EUA e oeste dos EUA), mas como elas têm o mesmo espaço de endereço, não é possível conectá-las juntas. 

## <a name="business-continuity"></a>Continuidade do Negócio

Pode haver várias maneiras diferentes de interromper o aplicativo. Uma região pode ser completamente cortada devido a um desastre natural ou a um desastre parcial, devido a uma falha de vários dispositivos ou serviços. O impacto no serviço VNet é diferente em cada uma dessas situações.

**P: Se ocorrer uma interrupção para uma região inteira, o que devo fazer? Por exemplo, se uma região for completamente cortada devido a um desastre natural? O que acontece com as redes virtuais hospedadas na região?**

R: A rede virtual e os recursos na região afetada permanecem inacessíveis durante o tempo de interrupção do serviço.

![Diagrama de rede virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: O que posso fazer para recriar a mesma rede virtual em uma região diferente?**

R: As redes virtuais são recursos bastante leves. Você pode invocar as APIs do Azure para criar uma VNet com o mesmo espaço de endereço em uma região diferente. Para recriar o mesmo ambiente que estava presente na região afetada, você faz chamadas à API para reimplantar as funções Web e de trabalho dos serviços de nuvem e as máquinas virtuais que você tinha. Se você tiver conectividade local, como em uma implantação híbrida, precisará implantar um novo gateway de VPN e conectar-se à sua rede local.

Para criar uma rede virtual, consulte [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network).

**P: Uma réplica de uma rede virtual em uma determinada região pode ser recriada em outra região antes do tempo?**

R: Sim, você pode criar duas VNets usando o mesmo espaço de endereço IP privado e recursos em duas regiões diferentes antes do tempo. Se você estiver hospedando serviços voltados para a Internet na VNet, você pode ter configurado o Gerenciador de tráfego para rotear o tráfego geograficamente para a região ativa. No entanto, você não pode conectar dois VNets com o mesmo espaço de endereço à sua rede local, pois isso causaria problemas de roteamento. No momento de um desastre e uma perda de uma VNet em uma região, você pode conectar a outra VNet na região disponível, com o espaço de endereço correspondente à sua rede local.

Para criar uma rede virtual, consulte [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network).

