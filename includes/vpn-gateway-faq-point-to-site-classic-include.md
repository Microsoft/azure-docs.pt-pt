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
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76309074"
---
Este FAQ aplica-se às ligações P2S que utilizam o modelo de implementação clássico.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Que sistemas operativos cliente posso utilizar com a ligação Ponto a Site?

São suportados os seguintes sistemas operativos cliente:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Posso usar qualquer cliente VPN de software que suporte sSTP para ponto-a-site?

Não. O suporte está limitado apenas às versões do sistema operativo Windows listadas.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Quantos pontos finais de cliente VPN podem existir na minha configuração Ponto-a-Site?

A quantidade de pontos finais do cliente VPN depende do seu gateway sku e protocolo.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?

Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar até 20 certificados de raiz.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Posso atravessar proxies e firewalls usando o Ponto-a-Local?

Sim. Utilizamos o Protocolo de Túnel de Tomada Segura (SSTP) para fazer um túnel através de firewalls. Este túnel aparece como uma ligação HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?

Por predefinição, o computador cliente não restabelecerá automaticamente a ligação VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>O Point-to-Site suporta a religação automática e o DDNS nos clientes VPN?

Não. A religação automática e o DDNS não são atualmente suportados em VPNs ponto-a-local.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Posso ter configurações site-to-site e ponto-a-site para a mesma rede virtual?

Sim. Ambas as soluções funcionarão se tiver um tipo VPN baseado em Rotação para o seu portal. No modelo de implementação clássica, precisa de um gateway dinâmico. Não apoiamos o Point-to-Site para encaminhamento estático de gateways OU gateways VPN que usam o cmdlet **-VpnType PolicyBased.**

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?

Sim. No entanto, as redes virtuais não podem ter prefixos IP sobrepostos e os espaços de endereço ponto-a-local não devem sobrepor-se entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?

É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. A entrada também é limitada pela latência e largura de banda entre as suas instalações e a internet.
