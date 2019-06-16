---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157679"
---
Nestas perguntas frequentes aplica-se às ligações de P2S que utilizam o modelo de implementação clássica.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Que sistemas operativos cliente posso utilizar com a ligação Ponto a Site?

São suportados os seguintes sistemas operativos cliente:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Pode utilizar qualquer cliente VPN de software que suporte SSTP para ligações ponto a Site?

Não. O suporte está limitado apenas para as versões de sistema operativo do Windows listadas.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN podem existir na minha configuração ponto a Site?

Até 128 VPN, os clientes podem ligar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar até 20 certificados de raiz.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Posso atravessar proxies e firewalls com o ponto a Site?

Sim. Usamos o Secure Socket Tunneling Protocol (SSTP) para atravessar as firewalls. Este túnel aparece como uma ligação HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?

Por predefinição, o computador cliente não restabelece a ligação VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Faz restabelecer a ligação automática de suporte ponto a Site e DDNS nos clientes VPN?

Não. Restabelecimento de ligação automático e DDNS não são atualmente suportados nas VPNs ponto a Site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Posso ter configurações de ponto a Site para a mesma rede virtual e de Site a Site?

Sim. Ambas as soluções funcionarão se tiver um tipo de RouteBased VPN para o seu gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não temos suporte ponto a Site para gateways de VPN de encaminhamento estático ou gateways que utilizem o **- VpnType PolicyBased** cmdlet.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?

Sim. No entanto, as redes virtuais não podem ter prefixos IP sobrepostos e os espaços de endereços de ponto a Site não pode sobrepor entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?

É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. O débito também é limitado pela latência e largura de banda entre o local e na internet.
