---
title: 'Azure ExpressRoute: Sobre encriptação'
description: Saiba mais sobre a encriptação ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461468"
---
# <a name="expressroute-encryption"></a>Encriptação ExpressRoute
 
O ExpressRoute suporta um par de tecnologias de encriptação para garantir a confidencialidade e integridade dos dados que atravessam entre a sua rede e a rede da Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Encriptação ponto-a-ponto por MACsec FAQ
MACsec é um [padrão do IEEE.](https://1.ieee802.org/security/802-1ae/) Encripta dados ao nível do controlo de acesso dos media (MAC) ou da Camada de Rede 2. Pode utilizar o MACsec para encriptar as ligações físicas entre os dispositivos de rede e os dispositivos de rede da Microsoft quando se ligar à Microsoft via [ExpressRoute Direct](expressroute-erdirect-about.md). O MACsec é desativado nas portas ExpressRoute Direct por padrão. Trazes a tua própria chave MACsec para encriptação e armazenas-a no [Cofre de Chaves Azure.](../key-vault/general/overview.md) Decide quando rodar a chave. Consulte outras FAQs abaixo.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Posso ativar o MACsec no meu circuito ExpressRoute, aprovisionado por um fornecedor expressRoute?
Não. A MACsec encripta todo o tráfego numa ligação física com uma chave detida por uma entidade (ou seja, cliente). Portanto, está disponível apenas no ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Posso encriptar alguns dos circuitos ExpressRoute nas minhas portas ExpressRoute Direct e deixar outros circuitos nas mesmas portas desencriptados? 
Não. Uma vez que o MACsec esteja habilitado todo o tráfego de controlo de rede, por exemplo, o tráfego de dados BGP, e o tráfego de dados dos clientes são encriptados. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando eu ativar/desativar a chave MACsec ou atualizar a chave MACsec perderá a conectividade com a Microsoft sobre o ExpressRoute?
Sim. Para a configuração do MACsec, suportamos apenas o modo chave pré-partilhado. Isto significa que precisa de atualizar a chave tanto nos seus dispositivos como nos da Microsoft (através da nossa API). Esta mudança não é atómica, por isso perderá a conectividade quando houver um desfasamento entre os dois lados. Recomendamos vivamente que marque uma janela de manutenção para a mudança de configuração. Para minimizar o tempo de inatividade, sugerimos que atualize a configuração numa ligação do ExpressRoute Direct num momento após mudar o tráfego da rede para o outro link.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>O tráfego continuará a fluir se houver uma incompatibilidade na chave MACsec entre os meus dispositivos e os da Microsoft?
Não. Se o MACsec estiver configurado e ocorrer um desfasamento chave, perde-se a conectividade com a Microsoft. Por outras palavras, não voltaremos a uma ligação não encriptada, expondo os seus dados. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Permitirá que o MACsec no ExpressRoute Direct degrade o desempenho da rede?
A encriptação e desencriptação do MACsec ocorrem em hardware nos routers que utilizamos. Não há impacto no desempenho do nosso lado. No entanto, deverá consultar o fornecedor de rede para os dispositivos que utiliza e verificar se o MACsec tem alguma implicação de desempenho.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>que suítes cifrasão são suportadas para encriptação?
Apoiamos a AES128 e a AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Encriptação de ponta a ponta por IPsec FAQ
IPsec é um [padrão IETF.](https://tools.ietf.org/html/rfc6071) Encripta dados ao nível do Protocolo de Internet (IP) ou da Camada de Rede 3. Pode utilizar o IPsec para encriptar uma ligação de ponta a ponta entre a sua rede no local e a sua rede virtual (VNET) no Azure. Consulte outras FAQs abaixo.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Posso permitir o IPsec para além do MACsec nas minhas portas ExpressRoute Direct?
Sim. O MACsec assegura as ligações físicas entre si e a Microsoft. O IPsec assegura a ligação de ponta a ponta entre si e as suas redes virtuais no Azure. Pode permitir-lhes de forma independente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Posso usar a porta de entrada Azure VPN para instalar o túnel IPsec entre a minha rede no local e a minha rede virtual Azure?
Sim. Pode configurar este túnel IPsec sobre o Microsoft Peering do seu circuito ExpressRoute. Siga o nosso guia de [configuração](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Posso usar a porta de entrada Azure VPN para montar o túnel IPsec sobre o Azure Private Peering?
Se adotar o Azure Virtual WAN, pode seguir [estes passos](../virtual-wan/vpn-over-expressroute.md) para encriptar a ligação de ponta a ponta. Se tiver um Azure VNET regular, pode implementar um gateway VPN de terceiros no seu VNET e estabelecer um túnel IPsec entre ele e o seu gateway VPN no local.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual é a entrada que vou obter depois de permitir o IPsec na minha ligação ExpressRoute?
Se for utilizado o gateway Azure VPN, verifique os [números](../vpn-gateway/vpn-gateway-about-vpngateways.md)de desempenho aqui . Se for utilizado um gateway VPN de terceiros, consulte o fornecedor para obter os números de desempenho.

## <a name="next-steps"></a>Passos seguintes
Consulte o [Configure MACsec](expressroute-howto-macsec.md) para obter mais informações sobre a configuração do MACsec.

Consulte o [Configure IPsec](site-to-site-vpn-over-microsoft-peering.md) para obter mais informações sobre a configuração do IPsec.
