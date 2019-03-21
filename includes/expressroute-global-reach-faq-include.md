---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 551b1d4924c354eb6fd501c1b82ed306a7ea4822
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305163"
---
### <a name="what-is-expressroute-global-reach"></a>O que é o alcance Global do ExpressRoute?

Alcance Global do ExpressRoute é um serviço do Azure que estabelece ligação as redes no local através do serviço de ExpressRoute através da rede global da Microsoft. Por exemplo, se tiver um centro de dados privados na Califórnia ligada ao ExpressRoute no vale do silício e outro Datacenter privada em Texas ligado ao ExpressRoute em Dallas, com o ExpressRoute alcance Global, pode ligar os centros de dados privados em conjunto por meio de ambas as ligações ExpressRoute e suas várias tráfego de centro de dados irá percorrer a backbone de rede da Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Como ativar ou desativar o alcance Global do ExpressRoute?

Ativar o alcance Global do ExpressRoute conectando seus circuitos do ExpressRoute. Desativar a funcionalidade ao desligar o circuitos. Consulte a [configuração](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>É necessário ExpressRoute Premium para alcance Global do ExpressRoute?

Se os circuitos do ExpressRoute estiverem na mesma região geopolítica, não precisa ExpressRoute Premium para ligá-los. Se dois circuitos do ExpressRoute estão em diferentes regiões geopolíticas, terá de ExpressRoute Premium para ambos os circuitos para habilitar a conectividade entre eles. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Como vou ser cobrado para alcance Global do ExpressRoute?

O ExpressRoute permite-conectividade da rede no local para serviços cloud da Microsoft. Alcance Global do ExpressRoute permite a conectividade entre suas próprias redes no local através de seus circuitos do ExpressRoute existentes, tirar partido da rede global da Microsoft. Alcance Global do ExpressRoute é cobrado separadamente do serviço de ExpressRoute existente. Existe uma taxa de suplemento para ativar esta funcionalidade em cada circuito ExpressRoute. Para uma taxa de saída na origem e para uma taxa de entrada no destino, será cobrado o tráfego entre as redes no local ativada por alcance Global do ExpressRoute. As tarifas baseiam-se na zona em que estão localizados os circuitos. Consulte <pricing page>

### <a name="where-is-expressroute-global-reach-supported"></a>Onde é alcance Global do ExpressRoute suportada?

Alcance Global do ExpressRoute é suportada no [selecionar países ou locais](../articles/expressroute/expressroute-global-reach.md). Os circuitos ExpressRoute têm de ser criados nas localizações peering nas locais ou países.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Posso ter mais de duas redes de locais, cada uma ligada a um circuito do ExpressRoute. Posso habilitar ExpressRoute alcance Global interligar todas as minhas redes no local?

Sim, pode, enquanto os circuitos estão em países/regiões suportados. Tem de se ligar dois circuitos do ExpressRoute por vez. Para criar uma rede de malha completa, terá de enumerar todos os pares de circuito e repita a configuração. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Posso habilitar alcance Global do ExpressRoute entre dois circuitos do ExpressRoute na mesma localização de peering?

Não. Os dois circuitos do tem de ser de diferentes localizações de peering. Se um metro num país suportado tiver mais de uma localização de peering do ExpressRoute, pode ligar em conjunto os circuitos do ExpressRoute criados em diferentes localizações de peering nesse metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Se o alcance Global do ExpressRoute estiver ativado entre o circuito X e Y do circuito e entre o circuito Y e Z do circuito, será minhas redes no local ligado ao circuito X e Z do circuito conversar entre si através de rede da Microsoft?

Não. Para ativar a conectividade entre quaisquer dois das suas redes no local, tem de ligar explicitamente os circuitos do ExpressRoute correspondentes. No exemplo acima, tem de ligar o circuito X e Z do circuito. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>O que é o débito de rede que pode esperar entre meu redes no local, depois, posso ativar o alcance Global do ExpressRoute?

O débito de rede entre as redes no local, ativada por ExpressRoute alcance Global, é colocado por mais pequeno de dois circuitos do ExpressRoute. Tráfego de local para o Azure e no local para local partilham o mesmo circuito e estão sujeitos ao mesmo limite de largura de banda. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Com o ExpressRoute alcance Global, quais são os limites no número de rotas, pode anunciar e o número de rotas que irá receber?

O número de rotas que pode anunciar à Microsoft no peering privado do Azure permanece em 4000 num circuito Standard ou 10000 num circuito Premium. O número de rotas que receberá da Microsoft no peering privado do Azure será a soma das rotas de redes virtuais do Azure e as rotas de suas outras redes no local ligada através do alcance Global do ExpressRoute. Certifique-se de que definir um limite máximo de prefixo adequado do router no local. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>O que é o SLA para alcance Global do ExpressRoute?

Alcance Global do ExpressRoute fornecerá o mesmo [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) como o serviço de ExpressRoute regular.
