---
title: Ligações VPN site-to-site sobre o espreitamento privado ExpressRoute
description: Este artigo ajuda-o a ativar a VPN site-to-site sobre o ExpressRoute a espreitar privadamente para encriptar o tráfego.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880530"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Configure uma ligação VPN site-to-site sobre o espreitamento privado ExpressRoute

Pode configurar uma VPN site-to-site para uma porta de entrada de rede virtual sobre um olho privado ExpressRoute usando um endereço IP RFC 1918. Esta configuração proporciona os seguintes benefícios:

* O tráfego sobre o espreitamento privado está encriptado.

* Os utilizadores ponto a local que se conectam a um gateway de rede virtual podem utilizar o ExpressRoute (através do túnel Site-To-Site) para aceder aos recursos no local.

>[!NOTE]
>Esta funcionalidade é suportada apenas em gateways redundantes de zona. Por exemplo, VpnGw1AZ, VpnGw2AZ, etc.
>

Para completar esta configuração, verifique se encontra os seguintes pré-requisitos:

* Tem um circuito ExpressRoute funcional que está ligado ao VNet onde o gateway VPN é (ou será) criado.

* Pode obter recursos sobre o IP RFC1918 (privado) no VNet sobre o circuito ExpressRoute.

## <a name="routing"></a><a name="routing"></a>Encaminhamento

**A Figura 1** mostra um exemplo de conectividade VPN sobre o espreitamento privado ExpressRoute. Neste exemplo, vê-se uma rede dentro da rede de instalações que está ligada ao gateway VPN hub Azure sobre o expressRoute private peering. Um aspeto importante desta configuração é o encaminhamento entre as redes no local e o Azure sobre os caminhos ExpressRoute e VPN.

Figura 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Figura 1":::

Estabelecer conectividade é simples:

1. Estabeleça a conectividade ExpressRoute com um circuito ExpressRoute e um espreitamento privado.

1. Estabeleça a conectividade VPN utilizando os passos deste artigo.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego das redes no local para Azure

Para o tráfego das redes no local para Azure, os prefixos Azure são anunciados através tanto do ExpressRoute private peering BGP, como do VPN BGP. O resultado são duas rotas de rede (caminhos) em direção a Azure a partir das redes no local:

• Uma rota de rede sobre o caminho protegido pelo IPsec.

• Uma rota de rede diretamente sobre o ExpressRoute sem proteção IPsec.

Para aplicar encriptação à comunicação, deve certificar-se de que para a rede ligada à VPN na **Figura 1**, as rotas Azure através do gateway VPN no local são preferíveis ao longo do caminho expressoroute direto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego de Azure para redes no local

O mesmo requisito aplica-se ao tráfego de Azure para redes no local. Para garantir que o caminho IPsec é preferido sobre o caminho expresso direto (sem IPsec), tem duas opções:

• **Anuncie prefixos mais específicos na sessão VPN BGP para a rede ligada à VPN**. Pode anunciar uma gama maior que engloba a rede ligada à VPN sobre o estomamento privado ExpressRoute, depois gamas mais específicas na sessão de BGP VPN. Por exemplo, anuncie 10.0.0.0/16 sobre o ExpressRoute e 10.0.0.0/24 sobre a VPN.

• **Anuncie prefixos de desarticulação para VPN e ExpressRoute**. Se as gamas de rede ligadas à VPN forem desarticuladas de outras redes conectadas com o ExpressRoute, pode anunciar os prefixos nas sessões BGP VPN e ExpressRoute, respectivamente. Por exemplo, anuncie 10.0.0.0/24 sobre o ExpressRoute e 10.0.0.0/24 sobre a VPN.

Em ambos os exemplos, a Azure enviará tráfego para 10.0.1.0/24 sobre a ligação VPN em vez de diretamente sobre ExpressRoute sem proteção VPN.

>[!Warning]
>Se publicitar os mesmos prefixos nas ligações ExpressRoute e VPN, >Azure utilizará o caminho ExpressRoute diretamente sem proteção VPN.
>

## <a name="portal-steps"></a><a name="portal"></a>Passos do portal

1. Configure uma ligação Site-to-Site. Para obter etapas, consulte o artigo [de configuração site-to-site.](./tutorial-site-to-site-portal.md) Certifique-se de escolher um gateway redundante de zona SKU para o portal. 

   SkUs redundantes de zona têm "AZ" no final do SKU. Por exemplo, **VpnGw1AZ**. Os gateways redundantes de zona só estão disponíveis em regiões onde o serviço de zona de disponibilidade está disponível. Para obter informações sobre as regiões em que apoiamos zonas de disponibilidade, consulte [Regiões que suportam zonas de disponibilidade.](../availability-zones/az-region.md)

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="IPs privados gateway":::
1. Ativar os IPs privados no gateway. Selecione **Configuração** e, em seguida, desemocione **os IPs privados** gateway para **ativados**. Selecione **Guardar** para guardar as alterações.
1. Na página **'Vista Geral',** selecione **Ver Mais** para ver o endereço IP privado. Escreva estas informações para utilizar mais tarde nos passos de configuração.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Página de descrição geral" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Para ativar **o endereço IP privado do Azure** na ligação, selecione  **Configuração**. **Desconfiem do endereço IP privado do Azure** para **ativar** e, em seguida, **selecione Guardar**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Gateway Private IPs - Habilitado":::
1. Da sua firewall, ping o IP privado que você anotou no passo 3. O IP privado deve ser acessível sobre o espreitamento privado ExpressRoute.
1. Utilize este IP privado como IP remoto na sua firewall no local para estabelecer o túnel Site-To-Site sobre o espreitamento privado ExpressRoute.

## <a name="powershell-steps"></a><a name="powershell"></a>Passos PowerShell

1. Configure uma ligação Site-to-Site. Para etapas, consulte o [Configure um artigo de VPN site-to-site.](./tutorial-site-to-site-portal.md) Certifique-se de escolher um gateway redundante de zona SKU para o portal. SkUs redundantes de zona têm "AZ" no final do SKU. Por exemplo, VpnGw1AZ.
1. Desaponhe a bandeira para utilizar o IP privado no gateway utilizando os seguintes comandos PowerShell:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Você deve ver um endereço IP público e privado. Anote o endereço IP na secção "TunnelIpAddresses" da saída. Usará esta informação mais tarde.
1. Desaponuse a ligação para utilizar o endereço IP privado utilizando o seguinte comando PowerShell:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Da sua firewall, ping o IP privado que você anotou no passo 2. Deve ser acessível sobre o olho privado ExpressRoute.
1. Utilize este IP privado como IP remoto na sua firewall no local para estabelecer o túnel Site-To-Site sobre o espreitamento privado ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o VPN Gateway, veja [o que é VPN Gateway?](vpn-gateway-about-vpngateways.md)