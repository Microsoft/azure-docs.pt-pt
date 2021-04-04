---
title: 'Azure ExpressRoute: Conecte-se à Microsoft Cloud usando o Global Reach'
description: Saiba como o Azure ExpressRoute Global Reach pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 50679e11697a4227af69b8568c5f3cd23fe26cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662802"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
O ExpressRoute é uma forma privada e resiliente de ligar as suas redes no local à Microsoft Cloud. Pode aceder a muitos serviços na nuvem da Microsoft, como o Azure e o Microsoft 365, a partir do seu centro de dados privado ou da sua rede corporativa. Por exemplo, você pode ter uma sucursal em São Francisco com um circuito ExpressRoute em Silicon Valley e outra filial em Londres com um circuito ExpressRoute na mesma cidade. Ambas as sucursais têm conectividade de alta velocidade com os recursos da Azure nos EUA Oeste e Reino Unido Sul. No entanto, as sucursais não podem ligar-se e enviar dados diretamente uns com os outros. Ou seja, 10.0.1.0/24 pode enviar dados para a rede 10.0.3.0/24 e 10.0.4.0/24, mas NÃO para a rede 10.0.2.0/24.

![Diagrama que mostra circuitos não ligados juntamente com a Express Route Global Reach.][1]

Com **o ExpressRoute Global Reach,** pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local. No exemplo acima, com a adição do ExpressRoute Global Reach, o seu escritório em São Francisco (10.0.0.0/24) pode trocar diretamente dados com o seu escritório em Londres (10.0.2.0/24) através dos circuitos ExpressRoute existentes e através da rede global da Microsoft. 

![Diagrama que mostra circuitos ligados em conjunto com a Express Route Global Reach.][2]

## <a name="use-case"></a>Caso de utilização
O ExpressRoute Global Reach foi concebido para complementar a implementação wan do seu fornecedor de serviços e ligar as suas sucursais em todo o mundo. Por exemplo, se o seu fornecedor de serviços operar principalmente nos Estados Unidos e tiver ligado todas as suas agências nos EUA, mas o prestador de serviços não operar no Japão e em Hong Kong, com o ExpressRoute Global Reach pode trabalhar com um fornecedor de serviços local e a Microsoft ligará as suas filiais lá às dos EUA usando a ExpressRoute e a nossa rede global.

![Diagrama que mostra um caso de uso para Express Route Global Reach.][3]

## <a name="availability"></a>Disponibilidade 
ExpressRoute Global Reach é suportado nos seguintes lugares. 

> [!NOTE] 
> Para permitir o ExpressRoute Global Reach entre [diferentes regiões geopolíticas,](expressroute-locations-providers.md#locations)os seus circuitos devem ser **Premium SKU**.

* Austrália
* Canadá
* França
* Alemanha
* R.A.E. de Hong Kong
* Irlanda
* Japão
* Coreia
* Países Baixos
* Nova Zelândia
* Noruega
* Singapura
* África do Sul (apenas Joanesburgo)
* Suécia
* Suíça
* Reino Unido
* Estados Unidos da América

## <a name="next-steps"></a>Passos seguintes
- Ver o [Global Reach FAQ](expressroute-faqs.md#globalreach).
- Saiba como [ativar](expressroute-howto-set-global-reach.md)o Global Reach .
- Saiba como [ligar um circuito ExpressRoute à sua rede virtual.](expressroute-howto-linkvnet-arm.md)

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "usar caso de alcance global"
