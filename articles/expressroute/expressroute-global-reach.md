---
title: Ligar a redes no local ao Microsoft Cloud com o alcance Global - Azure ExpressRoute | Documentos da Microsoft
description: Este artigo explica o alcance Global do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: e522c7ea306667f675af4bbe7486e25fb7f4ec9f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122807"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
O ExpressRoute é uma forma privada e resiliente para ligar as suas redes no local para a Cloud da Microsoft. Você pode acessar muitos serviços de nuvem da Microsoft, como o Azure, e o Office 365 de seu data center particular ou sua rede corporativa. Por exemplo, pode ter uma filial em são Francisco com um circuito do ExpressRoute no vale do silício e outra sucursal em Londres com um circuito do ExpressRoute na mesma cidade. Ambas as filiais podem ter conectividade de alta velocidade para recursos do Azure em E.u.a. oeste e sul do Reino Unido. No entanto, as filiais não podem trocar dados diretamente entre si. Em outras palavras, 10.0.1.0/24 pode enviar dados para 10.0.3.0/24 e 10.0.4.0/24, mas não a 10.0.2.0/24.

![sem][1]

Com o **alcance Global do ExpressRoute**, pode associar os circuitos do ExpressRoute em conjunto para garantir uma rede privada entre as redes no local. No exemplo acima, com a adição do ExpressRoute alcance Global, seu escritório de San Francisco (10.0.1.0/24) diretamente pode trocar dados com o seu escritório de Londres (10.0.2.0/24) por meio dos circuitos do ExpressRoute existentes e via rede global da Microsoft. 

![com o][2]

## <a name="use-case"></a>Caso de utilização
Alcance Global do ExpressRoute foi concebido para complementar a implementação de WAN do seu fornecedor de serviços e ligar suas filiais em todo o mundo. Por exemplo, se o seu fornecedor de serviços principalmente funciona nos Estados Unidos e tiver ligado a todos os seus ramos nos EUA, mas o fornecedor de serviços não funciona no Japão e RAE de Hong Kong, com alcance Global do ExpressRoute pode trabalhar com um fornecedor de serviços locais e Microsoft irá ligar os seus ramos existir nos Estados Unidos com o ExpressRoute e da nossa rede global.

![Caso de utilização][3]

## <a name="availability"></a>Disponibilidade 
Alcance Global do ExpressRoute atualmente é suportada nos seguintes locais.

* Austrália
* Canadá
* França
* Alemanha
* RAE de Hong Kong
* Irlanda
* Japão
* Coreia
* Países Baixos
* Singapura
* Suíça
* Reino Unido
* Estados Unidos

Os circuitos do ExpressRoute tem de ser criados no [localizações de peering de ExpressRoute](expressroute-locations.md) na região ou países acima. Para ativar o ExpressRoute alcance Global entre [diferentes regiões geopolíticas](expressroute-locations.md), seus circuitos tem de ser Premium SKU.

## <a name="next-steps"></a>Passos Seguintes
1. [Saiba mais sobre o alcance Global do ExpressRoute](expressroute-faqs.md)
2. [Como ativar o alcance Global do ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Ligar o circuito do ExpressRoute a rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "utilizar maiúsculas e minúsculas de alcance global"
