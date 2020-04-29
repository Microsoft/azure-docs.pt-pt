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
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183995"
---
### <a name="what-is-expressroute-global-reach"></a>O que é ExpressRoute Global Reach?

O ExpressRoute Global Reach é um serviço Azure que liga as suas redes no local através do serviço ExpressRoute através da rede global da Microsoft. Por exemplo, se tiver um centro de dados privado na Califórnia ligado ao ExpressRoute em Silicon Valley e a outro centro de dados privado no Texas ligado ao ExpressRoute em Dallas, com o ExpressRoute Global Reach, pode ligar os seus centros de dados privados através das duas ligações ExpressRoute e o tráfego do seu centro de dados cruzado sairá através da espinha dorsal da rede da Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Como posso ativar ou desativar o ExpressRoute Global Reach?

Ativa o ExpressRoute Global Reach ligando os circuitos ExpressRoute. Desativa a função desligando os circuitos. Veja a [configuração](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Preciso do ExpressRoute Premium para o ExpressRoute Global Reach?

Se os seus circuitos ExpressRoute estiverem na mesma região geopolítica, não precisa do ExpressRoute Premium para os ligar. Se dois circuitos ExpressRoute estiverem em diferentes regiões geopolíticas, precisa do ExpressRoute Premium para ambos os circuitos, de forma a permitir a conectividade entre eles. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Como vou ser cobrado pelo ExpressRoute Global Reach?

O ExpressRoute permite a conectividade desde a sua rede no local até aos serviços de cloud da Microsoft. O ExpressRoute Global Reach permite a conectividade entre as suas próprias redes no local através dos seus circuitos ExpressRoute existentes, alavancando a rede global da Microsoft. O ExpressRoute Global Reach é faturado separadamente do serviço ExpressRoute existente. Existe uma taxa adicional para ativar esta funcionalidade em cada circuito ExpressRoute. O tráfego entre as suas redes no local ativadas pelo ExpressRoute Global Reach será cobrado por uma taxa de saída na fonte e por uma taxa de ingresso no destino. As tarifas baseiam-se na zona em que os circuitos estão localizados.

### <a name="where-is-expressroute-global-reach-supported"></a>Onde é suportado o ExpressRoute Global Reach?

ExpressRoute Global Reach é suportado em [países/regiões ou locais selecionados.](../articles/expressroute/expressroute-global-reach.md) Os circuitos ExpressRoute devem ser criados nos locais de observação desses países/regiões ou locais.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Tenho mais de duas redes no local, cada uma ligada a um circuito ExpressRoute. Posso permitir que o ExpressRoute Global Reach ligue todas as minhas redes no local?

Sim, pode, desde que os circuitos estejam nos países/regiões apoiados. Precisa ligar dois circuitos ExpressRoute de cada vez. Para criar uma rede totalmente malhada, é necessário enumerar todos os pares de circuitos e repetir a configuração. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Posso permitir o ExpressRoute Global Reach entre dois circuitos ExpressRoute no mesmo local?

Não. Os dois circuitos devem ser de diferentes locais de observação. Se um metro num país/região apoiado tiver mais do que um local de observação expressRoute, pode ligar os circuitos ExpressRoute criados em diferentes locais de observação naquele metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Se o ExpressRoute Global Reach estiver ativado entre o circuito X e o circuito Y, e entre o circuito Y e o circuito Z, as minhas redes no local ligadas ao circuito X e o circuito Z falarão entre si através da rede da Microsoft?

Não. Para permitir a conectividade entre duas das suas redes no local, deve ligar explicitamente os circuitos ExpressRoute correspondentes. No exemplo acima, deve ligar o circuito X e o circuito Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Qual é o suporte da rede que posso esperar entre as minhas redes no local depois de permitir o ExpressRoute Global Reach?

A produção de rede entre as suas redes no local, ativada pelo ExpressRoute Global Reach, é limitada pelo menor dos dois circuitos ExpressRoute. O tráfego das instalações para o Azure e o tráfego de instalações para as instalações partilham o mesmo circuito e estão sujeitos à mesma tampa de largura de banda. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Com o ExpressRoute Global Reach, quais são os limites do número de rotas que posso anunciar e o número de rotas que vou receber?

O número de rotas que pode anunciar à Microsoft no Azure permanece em 4000 num circuito Standard ou 10000 num circuito Premium. O número de rotas que receberá da Microsoft no avistamento privado do Azure será a soma das rotas das suas redes virtuais Azure e as rotas das suas outras redes no local ligadas via ExpressRoute Global Reach. Certifique-se de que estabelece um limite máximo de prefixo adequado no seu router no local. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>O que é o SLA para o ExpressRoute Global Reach?

O ExpressRoute Global Reach fornecerá a mesma [disponibilidade SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) que o serviço expressRoute regular.
