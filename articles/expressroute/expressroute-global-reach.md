---
title: 'Azure ExpressRoute: Ligue-se à Cloud da Microsoft usando o Global Reach'
description: Este artigo explica o alcance Global do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538509"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
O ExpressRoute é uma forma privada e resiliente para ligar as suas redes no local para a Cloud da Microsoft. Pode aceder a muitos serviços na nuvem da Microsoft, como o Azure, e o Office 365 a partir do seu centro de dados privado ou da sua rede corporativa. Por exemplo, pode ter uma filial em são Francisco com um circuito do ExpressRoute no vale do silício e outra sucursal em Londres com um circuito do ExpressRoute na mesma cidade. Ambas as filiais podem ter conectividade de alta velocidade para recursos do Azure em E.u.a. oeste e sul do Reino Unido. No entanto, as filiais não podem trocar dados diretamente entre si. Em outras palavras, 10.0.1.0/24 pode enviar dados para 10.0.3.0/24 e 10.0.4.0/24, mas não a 10.0.2.0/24.

![sem][1]

Com o **ExpressRoute Global Reach,** pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local. No exemplo acima, com a adição do ExpressRoute alcance Global, seu escritório de San Francisco (10.0.1.0/24) diretamente pode trocar dados com o seu escritório de Londres (10.0.2.0/24) por meio dos circuitos do ExpressRoute existentes e via rede global da Microsoft. 

![com com][2]

## <a name="use-case"></a>Caso de utilização
Alcance Global do ExpressRoute foi concebido para complementar a implementação de WAN do seu fornecedor de serviços e ligar suas filiais em todo o mundo. Por exemplo, se o seu fornecedor de serviços principalmente funciona nos Estados Unidos e tiver ligado a todos os seus ramos nos EUA, mas o fornecedor de serviços não funciona no Japão e RAE de Hong Kong, com alcance Global do ExpressRoute pode trabalhar com um fornecedor de serviços locais e Microsoft irá ligar os seus ramos existir nos Estados Unidos com o ExpressRoute e da nossa rede global.

![caso de uso][3]

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
* Nova Zelândia
* Singapura
* Suíça
* Reino Unido
* Estados Unidos

Os seus circuitos ExpressRoute devem ser criados nos locais de observação da ExpressRoute nos países ou regiões acima [referidos.](expressroute-locations.md) Para permitir o ExpressRoute Global Reach entre [diferentes regiões geopolíticas,](expressroute-locations.md)os seus circuitos devem ser Premium SKU.

## <a name="next-steps"></a>Passos seguintes
1. [Saiba mais sobre o ExpressRoute Global Reach](expressroute-faqs.md)
2. [Como ativar o ExpressRoute Global Reach](expressroute-howto-set-global-reach.md)
3. [Circuito Link ExpressRoute para rede virtual Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "caso de uso de alcance global"
