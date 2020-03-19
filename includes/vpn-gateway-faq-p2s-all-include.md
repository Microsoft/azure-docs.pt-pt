---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ec684e5e6fa2ef8e9ed30be49f59e8aa7ef3a28b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117134"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quantos pontos finais de cliente VPN posso ter na minha configuração Ponto a Site?

Depende do SKU de gateway. Para obter mais informações sobre o número de ligações suportado, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Que sistemas operativos cliente posso utilizar com a ligação Ponto a Site?

São suportados os seguintes sistemas operativos cliente:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows Server 2016 (apenas 64 bits)
* Windows 10
* Mac OS X, versão 10.11 ou superior
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Posso atravessar proxies e firewalls com a capacidade Ponto a Site?

O Azure suporta três tipos de opções de VPN Ponto a site:

* Secure Socket Tunneling Protocol (SSTP). SSTP é uma solução baseada em SSL proprietária da Microsoft que consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP de saída que o SSL 443 utiliza.

* OpenVPN. OpenVPN é uma solução baseada em SSL que consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP de saída que o SSL 443 utiliza.

* VPN IKEv2. A VPN IKEv2 é uma solução VPN IPsec baseada em normas que utiliza portas UDP 500 e 4500 e o número do protocolo IP. 50. As firewalls nem sempre abrem estas portas, pelo que existe a possibilidade de a VPN IKEv2 não conseguir atravessar proxies e firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?

Por predefinição, o computador cliente não restabelece a ligação VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A ligação Ponto a Site suporta o restabelecimento de ligação automático e DDNS nos clientes VPN?

Atualmente, o restabelecimento de ligação automático e DDNS não são suportados nas VPNs Ponto a Site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações Site a Site e Ponto a Site coexistentes na mesma rede virtual?

Sim. Para o modelo de implementação do Resource Manager, tem de ter um tipo de VPN RouteBased para o seu gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não suportamos a ligação Ponto a Site para gateways de VPN de encaminhamento estático ou gateways de VPN PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a vários gateways de rede virtual ao mesmo tempo?

Dependendo do software de Cliente VPN utilizado, poderá conseguir ligar-se a vários Gateways de Rede Virtual desde que as redes virtuais a que se está a ligar não tenham espaços de endereços contraditórios entre eles ou a rede a partir da qual o cliente se está a ligar.  Embora o Azure VPN Client suporte muitas ligações VPN, apenas uma ligação pode estar Ligada num dado momento.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?

Sim, as ligações Ponto a Site a um Gateway de Rede Virtual implementado numa VNet que esteja em peering com outras VNets podem ter acesso a outras VNets em peering.  Desde que as VNets em peering estejam a utilizar as funcionalidades UseRemoteGateway/AllowGatewayTransit, o cliente Ponto a Site poderá ligar-se às VNets em peering.  Para obter mais informações, consulte [este](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) artigo.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?

É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. O débito também é limitado pela latência e pela largura de banda entre o local e a Internet. Para um Gateway de VPN com apenas ligações VPN Ponto a Site IKEv2, o débito total que pode esperar depende do SKU de Gateway. Para obter mais informações sobre o débito, veja [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Posso utilizar qualquer cliente VPN de software para a ligação Ponto a Site que suporte SSTP e/ou IKEv2?

Não. Só pode utilizar o cliente VPN nativo no Windows para SSTP e o cliente VPN nativo no Mac para IKEv2. No entanto, pode utilizar o cliente OpenVPN em todas as plataformas para se ligar através do protocolo OpenVPN. Veja a lista de sistemas operativos cliente suportados.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>O Azure suporta a VPN IKEv2 no Windows?

O IKEv2 é suportado no Windows 10 e Windows Server 2016. No entanto, para poder utilizar o IKEv2, tem de instalar as atualizações e definir uma chave de registo localmente. As versões de SO anteriores ao Windows 10 não são suportadas e só podem utilizar o SSTP ou o **Protocolo OpenVPN®**.

Para preparar o Windows 10 ou o Windows Server 2016 para o IKEv2:

1. Instale a atualização.

   | Versão do SO | Date | Número/Ligação |
   |---|---|---|
   | Windows Server 2016<br>Windows 10, Versão 1607 | 17 de janeiro de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, Versão 1703 | 17 de janeiro de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10, Versão 1709 | 22 de março, 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Defina o valor da chave de registo. Crie ou defina a chave "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload” REG_DWORD no registo como 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>O que acontece quando configuro o SSTP e o IKEv2 para ligações VPN P2S?

Quando configura o SSTP e o IKEv2 num ambiente misto (composto por dispositivos Windows e Mac), o cliente VPN do Windows tentará sempre utilizar o túnel IKEv2 em primeiro lugar, mas irá reverter para o SSTP se a ligação IKEv2 não tiver êxito. O MacOSX só liga através do IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Que outras plataformas não Windows e Mac suporta o Azure na VPN P2S?

O Azure suporta Windows, Mac e Linux para P2S VPN.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Já tenho um Gateway de VPN do Azure implementado. Posso ativar a VPN RADIUS e/ou IKEv2 no mesmo?

Sim, pode ativar estas novas funcionalidades nos gateways que já estejam implementados através do PowerShell ou do portal do Azure, desde que o SKU de gateway que estiver a utilizar suporte RADIUS e/ou IKEv2. Por exemplo, o SKU Básico do gateway de VPN não suporta RADIUS nem IKEv2.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Como posso remover a configuração de uma ligação P2S?

Pode remover uma configuração P2S com a CLI do Azure e o PowerShell através dos seguintes comandos:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
