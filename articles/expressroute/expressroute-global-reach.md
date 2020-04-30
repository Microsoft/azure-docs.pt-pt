---
title: 'Azure ExpressRoute: Ligue-se à Cloud da Microsoft usando o Global Reach'
description: Este artigo explica o ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 4c326a556530fd778c1178f11b79d2aba613b4c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136588"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
ExpressRoute é uma forma privada e resiliente de ligar as suas redes no local ao Microsoft Cloud. Pode aceder a muitos serviços na nuvem da Microsoft, como o Azure, e o Office 365 a partir do seu centro de dados privado ou da sua rede corporativa. Por exemplo, você pode ter uma sucursal em São Francisco com um circuito ExpressRoute em Silicon Valley e outra filial em Londres com um circuito ExpressRoute na mesma cidade. Ambas as sucursais podem ter conectividade de alta velocidade com os recursos Azure nos EUA West e UK South. No entanto, as sucursais não podem trocar dados diretamente entre si. Ou seja, 10.0.1.0/24 pode enviar dados para 10.0.3.0/24 e 10.0.4.0/24, mas NÃO para 10.0.2.0/24.

![sem][1]

Com o **ExpressRoute Global Reach,** pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local. No exemplo acima, com a adição do ExpressRoute Global Reach, o seu escritório em São Francisco (10.0.1.0/24) pode trocar diretamente dados com o seu escritório em Londres (10.0.2.0/24) através dos circuitos ExpressRoute existentes e através da rede global da Microsoft. 

![com com][2]

## <a name="use-case"></a>Caso de utilização
O ExpressRoute Global Reach foi concebido para complementar a implementação wan do seu fornecedor de serviços e ligar as suas filiais em todo o mundo. Por exemplo, se o seu fornecedor de serviços opera principalmente nos Estados Unidos e ligou todas as suas agências nos EUA, mas o prestador de serviços não opera no Japão e em Hong Kong, com o ExpressRoute Global Reach pode trabalhar com um prestador de serviços local e a Microsoft irá ligar as suas agências lá às dos EUA usando a ExpressRoute e a nossa rede global.

![caso de uso][3]

## <a name="availability"></a>Disponibilidade 
A ExpressRoute Global Reach é atualmente suportada nos seguintes locais.

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
1. [Ver o Global Reach FAQ](expressroute-faqs.md#globalreach)
2. [Saiba como ativar o Alcance Global](expressroute-howto-set-global-reach.md)
3. [Saiba como ligar um circuito ExpressRoute à sua rede virtual](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "caso de uso de alcance global"
