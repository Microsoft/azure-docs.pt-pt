---
title: 'Azure ExpressRoute: sobre criptografia'
description: Saiba mais sobre a criptografia do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: 7dd9106539b6756d74629ac663241a5b5562cefb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437050"
---
# <a name="expressroute-encryption"></a>Criptografia do ExpressRoute
 
O ExpressRoute dá suporte a algumas tecnologias de criptografia para garantir a confidencialidade e a integridade da passagem de dados entre a rede e a rede da Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Perguntas frequentes sobre criptografia ponto a ponto do MACsec
MACsec é um [padrão IEEE](https://1.ieee802.org/security/802-1ae/). Ele criptografa dados no nível MAC (controle de acesso à mídia) ou na camada de rede 2. Você pode usar o MACsec para criptografar os links físicos entre os dispositivos de rede e os dispositivos de rede da Microsoft ao se conectar à Microsoft por meio do [ExpressRoute Direct](expressroute-erdirect-about.md). O MACsec é desabilitado em portas diretas do ExpressRoute por padrão. Você traga sua própria chave MACsec para criptografia e a armazena em [Azure Key Vault](../key-vault/key-vault-overview.md). Você decide quando girar a chave. Veja outras perguntas frequentes abaixo.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Posso habilitar MACsec em meu circuito do ExpressRoute provisionado por um provedor de ExpressRoute?
Não. O MACsec criptografa todo o tráfego em um link físico com uma chave de propriedade de uma entidade (ou seja, cliente). Portanto, ele está disponível somente no ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Posso criptografar alguns dos circuitos do ExpressRoute em minhas portas diretas do ExpressRoute e deixar outros circuitos nas mesmas portas descriptografadas? 
Não. Quando o MACsec está habilitado, todo o tráfego de controle de rede, por exemplo, o tráfego de dados BGP e o tráfego de dados do cliente são criptografados. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Quando eu habilitar/desabilitar MACsec ou atualizar a chave MACsec, minha rede local perderá a conectividade com a Microsoft por meio do ExpressRoute?
Sim. Para a configuração MACsec, damos suporte apenas ao modo de chave pré-compartilhada. Isso significa que você precisa atualizar a chave em ambos os dispositivos e na Microsoft (por meio de nossa API). Essa alteração não é atômica, portanto, você perderá a conectividade quando houver uma incompatibilidade de chave entre os dois lados. É altamente recomendável que você agende uma janela de manutenção para a alteração de configuração. Para minimizar o tempo de inatividade, sugerimos que você atualize a configuração em um link do ExpressRoute Direct por vez, depois de alternar o tráfego de rede para o outro link.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>O tráfego continuará a fluir se houver uma incompatibilidade na chave MACsec entre meus dispositivos e a Microsoft?
Não. Se MACsec estiver configurado e ocorrer uma incompatibilidade de chave, você perderá a conectividade com a Microsoft. Em outras palavras, não retornaremos a uma conexão não criptografada, expondo seus dados. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Permitirá que o MACsec no ExpressRoute direcione o desempenho da rede?
A criptografia e a descriptografia do MACsec ocorrem no hardware nos roteadores que usamos. Não há impacto no desempenho em nosso lado. No entanto, você deve verificar com o fornecedor de rede os dispositivos que você usa e ver se MACsec tem alguma implicação de desempenho.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>quais conjuntos de codificação têm suporte para criptografia?
Damos suporte a AES128 e AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Perguntas frequentes sobre criptografia de ponta a ponta por IPsec
O IPsec é um [padrão IETF](https://tools.ietf.org/html/rfc6071). Ele criptografa dados no nível do protocolo IP ou na camada de rede 3. Você pode usar o IPsec para criptografar uma conexão de ponta a ponta entre sua rede local e sua rede virtual (VNET) no Azure. Veja outras perguntas frequentes abaixo.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Posso habilitar o IPsec além do MACsec em minhas portas diretas do ExpressRoute?
Sim. O MACsec protege as conexões físicas entre você e a Microsoft. O IPsec protege a conexão de ponta a ponta entre você e suas redes virtuais no Azure. Você pode habilitá-los independentemente. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Posso usar o gateway de VPN do Azure para configurar o túnel IPsec entre minha rede local e minha rede virtual do Azure?
Sim. Você pode configurar esse túnel IPsec sobre o emparelhamento da Microsoft de seu circuito do ExpressRoute. Siga nosso [Guia de configuração](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Posso usar o gateway de VPN do Azure para configurar o túnel IPsec sobre o emparelhamento privado do Azure?
Se você adotar a WAN virtual do Azure, poderá seguir [estas etapas](../virtual-wan/vpn-over-expressroute.md) para criptografar a conexão de ponta a ponta. Se você tiver uma VNET do Azure regular, poderá implantar um gateway de VPN de terceiros em sua VNET e estabelecer um túnel IPsec entre ele e o gateway de VPN local.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Qual será a taxa de transferência que receberei depois de habilitar o IPsec em minha conexão do ExpressRoute?
Se o gateway de VPN do Azure for usado, verifique os [números de desempenho aqui](../vpn-gateway/vpn-gateway-about-vpngateways.md). Se um gateway de VPN de terceiros for usado, verifique com o fornecedor os números de desempenho.

## <a name="next-steps"></a>Passos seguintes
Consulte [Configurar MACsec](expressroute-howto-macsec.md) para obter mais informações sobre a configuração do MACsec.

Consulte [Configurar IPSec](site-to-site-vpn-over-microsoft-peering.md) para obter mais informações sobre a configuração de IPSec.
