---
title: 'Azure ExpressRoute: Sobre a Encriptação'
description: Saiba mais sobre a encriptação ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: duau
ms.openlocfilehash: 46f0a0e86c5db612f440bcf631329d2800251dab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397802"
---
# <a name="expressroute-encryption"></a>Encriptação ExpressRoute
 
O ExpressRoute suporta algumas tecnologias de encriptação para garantir a confidencialidade e integridade dos dados que atravessam a sua rede e a rede da Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Encriptação ponto-a-ponto por FAQ MACsec
O MACsec é uma [norma IEEE.](https://1.ieee802.org/security/802-1ae/) Encripta dados no nível de controlo de acesso ao mídia (MAC) ou na Camada de Rede 2. Pode utilizar o MACsec para encriptar as ligações físicas entre os seus dispositivos de rede e os dispositivos de rede da Microsoft quando se ligar à Microsoft via [ExpressRoute Direct](expressroute-erdirect-about.md). O MACsec é desativado nas portas ExpressRoute Direct por padrão. Você traz a sua própria chave MACsec para encriptação e armazene-a no [Azure Key Vault](../key-vault/general/overview.md). Decide quando rodar a chave. Veja outras PERGUNTAS Frequentes abaixo.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Posso permitir que o MACsec no meu circuito ExpressRoute seja aprovisionado por um fornecedor ExpressRoute?
N.º O MACsec encripta todo o tráfego numa ligação física com uma chave detida por uma entidade (ou seja, cliente). Portanto, está disponível apenas no ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Posso encriptar alguns dos circuitos ExpressRoute nas minhas portas ExpressRoute Direct e deixar outros circuitos nas mesmas portas sem encriptação? 
N.º Uma vez que o MACsec esteja ativado todo o tráfego de controlo de rede, por exemplo, o tráfego de dados BGP e o tráfego de dados do cliente são encriptados. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando eu ativar/desativar o MACsec ou atualizar a chave MACsec, a minha rede no local perderá a conectividade com a Microsoft em vez do ExpressRoute?
Sim. Para a configuração DO MACsec, apoiamos apenas o modo de chave pré-partilhado. Significa que precisa de atualizar a chave tanto nos seus dispositivos como na microsoft (através da nossa API). Esta mudança não é atómica, por isso vai perder conectividade quando há um desfasamento entre os dois lados. Recomendamos vivamente que marque uma janela de manutenção para a alteração da configuração. Para minimizar o tempo de inatividade, sugerimos que atualize a configuração de uma ligação do ExpressRoute Direct numa altura em que altere o tráfego da rede para outra ligação.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>O tráfego continuará a fluir se houver uma incompatibilidade na chave MACsec entre os meus dispositivos e os da Microsoft?
N.º Se o MACsec estiver configurado e ocorrer uma incompatibilidade de chaves, perde-se a conectividade com a Microsoft. Por outras palavras, não vamos voltar a uma ligação não encriptada, expondo os seus dados. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Permitirá que o MACsec no ExpressRoute Direct degrade o desempenho da rede?
A encriptação e desencriptação do MACsec ocorre em hardware nos routers que utilizamos. Não há impacto no nosso lado. No entanto, deverá consultar o fornecedor de rede para os dispositivos que utiliza e ver se o MACsec tem alguma implicação de desempenho.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Que suítes cifra são suportadas para encriptação?
Apoiamos apenas a versão [de Numeramento de Pacotes Estendidos](https://1.ieee802.org/security/802-1aebw/) de AES128 e AES256. Além disso, desative o [Identificador de Canal Seguro (SCI)](https://en.wikipedia.org/wiki/IEEE_802.1AE) na configuração DO MACsec no seu dispositivo. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Encriptação de ponta a ponta por FAQ IPsec
O IPsec é uma [norma IETF.](https://tools.ietf.org/html/rfc6071) Encripta dados no nível do Protocolo de Internet (IP) ou na Camada de Rede 3. Pode utilizar o IPsec para encriptar uma ligação de ponta a ponta entre a sua rede no local e a sua rede virtual (VNET) no Azure. Veja outras PERGUNTAS Frequentes abaixo.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Posso permitir o IPsec para além do MACsec nas minhas portas ExpressRoute Direct?
Sim. O MACsec assegura as ligações físicas entre si e a Microsoft. O IPsec assegura a ligação de ponta a ponta entre si e as suas redes virtuais no Azure. Pode ative-los independentemente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Posso usar a porta de entrada da Azure VPN para instalar o túnel IPsec entre a minha rede no local e a minha rede virtual Azure?
Sim. Pode configurar este túnel IPsec sobre o Microsoft Peering do seu circuito ExpressRoute. Siga o nosso [guia de configuração.](site-to-site-vpn-over-microsoft-peering.md)
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Posso usar a porta de entrada da Azure VPN para montar o túnel IPsec sobre o Azure Private Peering?
Se adotar a Azure Virtual WAN, pode seguir [estes passos](../virtual-wan/vpn-over-expressroute.md) para encriptar a ligação de ponta a ponta. Se tiver um VNET Azure regular, pode implantar uma porta VPN de terceiros no seu VNET e estabelecer um túnel IPsec entre ele e o seu gateway VPN no local.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual é a produção que vou ter depois de permitir o IPsec na minha ligação ExpressRoute?
Se o gateway Azure VPN for utilizado, verifique os [números](../vpn-gateway/vpn-gateway-about-vpngateways.md)de desempenho aqui . Se for utilizado um gateway VPN de terceiros, verifique com o fornecedor os números de desempenho.

## <a name="next-steps"></a>Passos seguintes
Consulte [o CONFIGURAR MACsec](expressroute-howto-macsec.md) para obter mais informações sobre a configuração do MACsec.

Consulte [o IPsec configurar](site-to-site-vpn-over-microsoft-peering.md) para obter mais informações sobre a configuração do IPsec.
