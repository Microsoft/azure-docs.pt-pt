---
title: Rede de solução VMware Azure e conectividade
description: Azure VMware Solution networking e descrição da conectividade.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462592"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Azure VMware Solution oferece um ambiente de nuvem privada acessível a partir de instalações e recursos baseados em Azure. Serviços como Azure ExpressRoute, ligações VPN ou Azure Virtual WAN fornecem a conectividade. Estes serviços requerem gamas específicas de endereços de rede e portas de firewall para permitir os serviços.

Ao implementar uma nuvem privada, são criadas redes privadas de gestão, provisionamento e vMotion. Utilize estas redes privadas para aceder ao vCenter e ao NSX-T Manager e à máquina virtual vMotion ou implementação.  

O ExpressRoute Global Reach é usado para ligar nuvens privadas a ambientes no local. A ligação requer uma rede virtual com um circuito ExpressRoute para as instalações da sua subscrição.

As máquinas virtuais (VMs) implantadas na nuvem privada são acessíveis à internet através da funcionalidade IP pública Azure Virtual WAN.  Por padrão, o acesso à Internet é desativado para novas nuvens privadas. Para obter mais informações, consulte [como utilizar a funcionalidade IP pública na Solução VMware Azure.](../public-ip-usage.md)

