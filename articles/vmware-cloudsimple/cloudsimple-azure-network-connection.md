---
title: Solução VMware by CloudSimple - Ligações de rede Azure
description: Saiba como ligar a sua rede virtual Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025083"
---
# <a name="azure-network-connections-overview"></a>Visão geral das ligações da rede Azure

Quando cria um serviço CloudSimple numa região e cria nómadas, pode:

* Solicite um circuito Azure ExpressRoute e prenda-o à rede CloudSimple naquela região.
* Ligue a sua rede de região CloudSimple à sua rede virtual Azure ou à sua rede no local utilizando o Azure ExpressRoute.
* Fornecer acesso a serviços em execução na sua subscrição Azure ou na sua rede no local a partir do seu ambiente Private Cloud.

A ligação ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Benefícios

A ligação à rede Azure permite:00:

* Utilize o Azure como alvo de reserva para máquinas virtuais na sua Nuvem Privada.
* Implemente servidores KMS na sua subscrição Azure para encriptar a sua loja de dados Private Cloud vSAN.
* Utilize aplicações híbridas onde o nível web da aplicação é executado na nuvem pública enquanto os níveis de aplicação e base de dados funcionam na sua Nuvem Privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual Azure

Nuvens Privadas podem ser ligadas aos seus recursos Azure usando o ExpressRoute.  A ligação ExpressRoute permite-lhe aceder aos recursos em execução na sua subscrição Azure a partir da sua Cloud Privada.  Esta ligação permite-lhe estender a sua rede Private Cloud à sua rede virtual Azure.  As rotas da rede CloudSimple serão trocadas com a sua rede virtual Azure via BGP.  Se tiver uma rede virtual a espreitar configurada, todas as redes virtuais com vista estarão acessíveis a partir da sua rede CloudSimple.

![Ligação Azure ExpressRoute à rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Ligação ExpressRoute à rede de instalações

Pode ligar o circuito Azure ExpressRoute existente à região CloudSimple. A função ExpressRoute Global Reach é utilizada para ligar os dois circuitos entre si.  Estabelece-se uma ligação entre os circuitos CloudSimple ExpressRoute.  Esta ligação permite-lhe estender as suas redes no local à rede Private Cloud. As rotas da sua rede CloudSimple serão trocadas via BGP com a sua rede no local.

![Ligação ExpressRoute - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Ligação à rede no local e rede virtual Azure

As ligações à rede no local e à rede virtual Azure podem coexistir a partir da sua rede CloudSimple.  A ligação utiliza o BGP para trocar rotas entre a rede no local, a rede virtual Azure e a rede CloudSimple.  Quando ligar a sua rede CloudSimple à sua rede virtual Azure na presença da ligação Global Reach, as rotas de rede virtual Azure estarão visíveis na sua rede no local.  A troca de rotas acontece em Azure entre os routers de borda.

![Ligação expressRoute no local com ligação de rede virtual Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerações importantes

A ligação à rede CloudSimple a partir da rede no local e da rede virtual Azure permite a troca de rotas entre todas as redes.

* A rede virtual Azure será visível tanto a partir da rede no local como da rede CloudSimple.
* Se tiver ligado à sua rede virtual Azure a partir da rede no local, a ligação à rede CloudSimple utilizando o Global Reach permitirá o acesso a redes virtuais a partir da rede CloudSimple.
* Os endereços de **sub-rede não devem** sobrepor-se entre nenhuma das redes ligadas.
* CloudSimple **não** anunciará rota padrão para as ligações ExpressRoute
* Se o seu router no local anunciar a rota padrão, o tráfego da rede CloudSimple e da rede virtual Azure utilizará a rota padrão anunciada.  Como resultado, as máquinas virtuais em Azure não podem ser acedidas usando endereços IP públicos.

## <a name="next-steps"></a>Passos seguintes

* [Ligue a rede virtual Azure à CloudSimple usando o ExpressRoute](virtual-network-connection.md)
* [Ligue-se de instalações para CloudSimple usando ExpressRoute](on-premises-connection.md)
