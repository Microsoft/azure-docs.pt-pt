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
ms.openlocfilehash: 5c663bb507656dad6f073b9906fdbf104714c8f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450814"
---
Estas perguntas frequentes se aplicam a conexões P2S que usam o modelo de implantação clássico.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Que sistemas operativos cliente posso utilizar com a ligação Ponto a Site?

São suportados os seguintes sistemas operativos cliente:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Posso usar qualquer cliente VPN de software que dê suporte a SSTP para ponto a site?

Não. O suporte é limitado apenas às versões listadas do sistema operacional Windows.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade do cliente VPN podem existir em minha configuração de ponto a site?

A quantidade de pontos de extremidade do cliente VPN depende do seu protocolo e SKU do gateway.
[! INCLUIR taxa de transferência agregada por SKU]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Você ainda pode carregar até 20 certificados raiz.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Posso atravessar proxies e firewalls usando ponto a site?

Sim. Usamos o protocolo SSTP para encapsular por meio de firewalls. Esse túnel aparece como uma conexão HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>O ponto a site dá suporte a reconexão automática e DDNS nos clientes VPN?

Não. A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site para a mesma rede virtual?

Sim. Ambas as soluções funcionarão se você tiver um tipo de VPN RouteBased para seu gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não há suporte para ponto a site para gateways de VPN de roteamento estático ou gateways que usam o cmdlet **-VpnType PolicyBased** .

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?

Sim. No entanto, as redes virtuais não podem ter prefixos de IP sobrepostos e os espaços de endereço ponto a site não devem se sobrepor entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?

É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. A taxa de transferência também é limitada pela latência e pela largura de banda entre seu local e a Internet.
