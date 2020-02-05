---
title: Soluções VMware (AVS) - Ligações de rede Azure
description: Saiba ligar a sua rede virtual Azure à sua rede da região AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025083"
---
# <a name="azure-network-connections-overview"></a>Visão geral das ligações da rede Azure

Quando se cria um serviço AVS numa região e cria nós, pode:

* Solicite um circuito Azure ExpressRoute e prenda-o à rede AVS naquela região.
* Ligue a sua rede da região AVS à sua rede virtual Azure ou à sua rede no local utilizando o Azure ExpressRoute.
* Forneça acesso a serviços em execução na sua subscrição Azure ou à sua rede no local a partir do seu ambiente Private Cloud.

A ligação ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Vantagens

A ligação à rede Azure permite::

* Utilize o Azure como alvo de reserva para máquinas virtuais na sua Nuvem Privada.
* Implemente servidores KMS na sua subscrição Azure para encriptar a sua loja de dados Private Cloud vSAN.
* Utilize aplicações híbridas onde o nível web da aplicação funciona na Nuvem Pública AVS enquanto os níveis de aplicação e base de dados funcionam na sua Nuvem Privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual Azure

As Nuvens Privadas AVS podem ser ligadas aos seus recursos Azure utilizando o ExpressRoute. A ligação ExpressRoute permite-lhe aceder a recursos que estão a funcionar na subscrição do Azure a partir da sua Nuvem Privada AVS. Esta ligação permite-lhe estender a sua rede AVS Private Cloud à sua rede virtual Azure. As rotas de uma rede AVS serão trocadas com a sua rede virtual Azure via BGP. Se tiver o peering de rede virtual configurado, todas as redes virtuais serão acessíveis a partir da sua rede AVS.

![Ligação Azure ExpressRoute à rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Ligação ExpressRoute à rede no local

Pode ligar o circuito Azure ExpressRoute existente à sua região AVS. A funcionalidade ExpressRoute Global Reach é utilizada para ligar os dois circuitos entre si. Estabelece-se uma ligação entre os circuitos acionais e os circuitos AVS ExpressRoute. Esta ligação permite-lhe estender as suas redes no local a uma rede AVS Private Cloud. As rotas da sua rede AVS serão trocadas via BGP com a sua rede no local.

![Ligação ExpressRoute no local - Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Ligação à rede no local e à rede virtual Azure

As ligações à rede no local e à rede virtual Azure podem coexistir a partir da sua rede AVS. A ligação utiliza o BGP para trocar rotas entre uma rede no local, uma rede virtual Azure e uma rede AVS. Quando ligar a sua rede AVS à sua rede virtual Azure na presença de uma ligação Global Reach, as rotas de rede virtual Azure serão visíveis na sua rede no local. A troca de rotas acontece em Azure entre os routers de borda.

![Ligação expressroute no local com ligação à rede virtual Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerações importantes

A ligação a uma rede AVS a partir de uma rede no local e/ou de uma rede virtual Azure permite o intercâmbio de rotas entre todas as redes.

* Uma rede virtual Azure será visível tanto a partir de uma rede no local como de uma rede AVS.
* Se tiver ligado à sua rede virtual Azure a partir de uma rede no local, a ligação a uma rede AVS utilizando o Global Reach permitirá o acesso a redes virtuais a partir da rede AVS.
* Os endereços da sub-rede **não devem** sobrepor-se entre nenhuma das redes ligadas.
* AVS **não** anunciará uma rota padrão para as ligações ExpressRoute
* Se o seu router no local anunciar a rota padrão, o tráfego da rede AVS e da rede virtual Azure utilizará a rota padrão anunciada. Como resultado, as máquinas virtuais em Azure não podem ser acedidas através de endereços IP públicos.

## <a name="next-steps"></a>Passos seguintes

* [Ligue a rede virtual Azure ao AVS utilizando o ExpressRoute](virtual-network-connection.md)
* [Ligue-se a partir de instalações para AVS usando ExpressRoute](on-premises-connection.md)
