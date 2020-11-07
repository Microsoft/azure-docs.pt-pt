---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359508"
---
* Tem uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o artigo [Quickstart.](../articles/virtual-network/quick-create-portal.md)

* A sua rede virtual não deve ter quaisquer portas de rede virtuais existentes. Se a sua rede virtual já tiver gateways (VPN ou ExpressRoute), deve remover todos os gateways antes de prosseguir. Esta configuração requer que as redes virtuais se conectem apenas ao gateway do hub Virtual WAN.

* Um hub virtual é uma rede virtual que é criada e usada pela Virtual WAN. É o núcleo da sua rede Virtual WAN numa região. Obtenha um intervalo de endereços IP para a sua região de hub virtual. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das redes virtuais existentes a que se conecta. Também não pode sobrepor-se aos intervalos de endereços no local a que se conecta. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.
