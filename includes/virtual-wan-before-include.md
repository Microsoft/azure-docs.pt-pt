---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812732"
---
* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [arranque rápido](../articles/virtual-network/quick-create-portal.md).

* A sua rede virtual não tem quaisquer portas de rede virtuais. Se a sua rede virtual tiver um gateway (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao gateway do hub Virtual WAN.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
