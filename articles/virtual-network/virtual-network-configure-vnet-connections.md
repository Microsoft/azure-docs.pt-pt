---
title: Configurar e validar ligações de rede virtual ou VPN
description: Orientação passo a passo para configurar e validar várias implementações de VPN Esuráure e redes virtuais
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9a0d06a8f8fa8f68f063404f2b483b817eb0563f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452106"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configurar e validar ligações de rede virtual ou VPN

Este walkthrough fornece orientação passo a passo para configurar e validar várias implementações de VPN Azure e redes virtuais. Os cenários incluem o encaminhamento de trânsito, ligações rede-rede, Protocolo border gateway (BGP), ligações multissitas e ligações ponto-a-local.

Os gateways Azure VPN permitem flexibilidade na organização de quase qualquer tipo de topologia de rede virtual conectada em Azure. Por exemplo, pode ligar redes virtuais:

- Através de regiões.
- Entre tipos de rede virtuais (Azure Resource Manager versus classic).
- Dentro de Azure ou dentro de um ambiente híbrido no local.
- Em diferentes subscrições. 

## <a name="network-to-network-vpn-connection"></a>Ligação VPN rede-a-rede

Ligar uma rede virtual a outra rede virtual (rede-a-rede) via VPN é semelhante à ligação de uma rede virtual a uma localização do site no local. Ambos os tipos de conectividade usam uma porta de entrada VPN para fornecer um túnel seguro através do IPsec e do IKE. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes.

![Ligação rede-em-rede com o IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se as suas redes virtuais estiverem na mesma região, talvez deva considerar conectá-las utilizando o olhar de rede virtual. O olhar de rede virtual não usa um gateway VPN. Aumenta a produção e diminui a latência. Para configurar uma ligação de observação de rede virtual, selecione **Configurar e valide VNet Peering**.

Se as suas redes virtuais foram criadas através do modelo de implementação da Azure Resource Manger, selecione **Configure e valide um VNet do Gestor de Recursos para uma ligação VNet do Gestor de Recursos** para configurar uma ligação VPN.

Se uma das redes virtuais foi criada através do modelo de implementação clássica do Azure, e a outra foi criada através do Resource Manager, selecione **Configure e valide um VNet clássico para uma ligação VNet do Gestor de Recursos** para configurar uma ligação VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configurar rede virtual que espreita para duas redes virtuais na mesma região

Antes de começar a implementar e configurar o esquisite da rede virtual Azure, certifique-se de que cumpre os seguintes pré-requisitos:

* As redes virtuais no modo de peering têm de estar na mesma região do Azure.
* As redes virtuais espreitadas devem ter espaços de endereço IP que não se sobrepõem.
* O peering de rede virtual é feito entre duas redes virtuais. Não há uma relação transitiva derivada entre os seus olhos. Por exemplo, se o VNetA for espremiado com VNetB, e o VNetB for esprevado com VNetC, o VNetA *não* é espremiado com VNetC.

Quando cumprir os requisitos, pode seguir [Tutorial: Ligar redes virtuais com redes virtuais, utilizando o portal Azure](./tutorial-connect-virtual-networks-portal.md) para criar e configurar o espreitamento.

Para verificar a configuração de peering, utilize o seguinte método:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém o texto **Procurar recursos** no topo do portal, digite **redes virtuais.** Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Na lâmina de **redes Virtuais** que aparece, selecione a rede virtual para a qual pretende criar um espreitamento.
4. No painel que aparece para a rede virtual, **selecione Peerings** na secção **Definições.**
5. Selecione um olhar e veja os resultados da configuração.

![Seleções para verificar a configuração de peering da rede virtual](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Para a Azure PowerShell, executar o comando [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) para obter o olhar da rede virtual. Eis um exemplo:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Ligue uma rede virtual do Gestor de Recursos a outra rede virtual do Gestor de Recursos

Pode configurar uma ligação de uma rede virtual do Gestor de Recursos para outra rede virtual do Gestor de Recursos diretamente. Ou pode configurar a ligação utilizando o IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configurar uma ligação VPN entre redes virtuais do Gestor de Recursos

Para configurar uma ligação entre as redes virtuais do Gestor de Recursos sem IPsec, consulte [configurar uma ligação de gateway VPN rede-rede utilizando o portal Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

Para configurar uma ligação com o IPsec entre duas redes virtuais do Gestor de Recursos, siga os passos 1 a 5 na [Criar uma ligação site-a-local no portal Azure](../vpn-gateway/tutorial-site-to-site-portal.md) para cada rede virtual.

> [!Note]
> Estes passos funcionam apenas para redes virtuais na mesma subscrição. Se as suas redes virtuais estiverem em diferentes subscrições, deve utilizar o PowerShell para escoar a ligação. Consulte o artigo [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Validar a ligação VPN entre redes virtuais do Gestor de Recursos

![Ligação virtual clássica à rede virtual Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Para verificar se a sua ligação VPN está configurada corretamente, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtual nestes passos correspondem aos números do diagrama anterior.

1. Certifique-se de que não existem espaços de endereço sobreposto nas redes virtuais ligadas.
2. Verifique se o intervalo de endereços da rede virtual Azure Resource Manager (1) é definido com precisão na instância do **objeto De ligação** (4).
3. Verifique se o intervalo de endereços da rede virtual Azure Resource Manager (6) é definido com precisão na instância do **objeto De ligação** (3).
4. Verifique se as teclas pré-partilhadas estão a coincidir com os objetos de ligação.
5. Verifique se o gateway de rede virtual VIP (2) do Azure Resource Manager é definido com precisão na instância do **objeto de Ligação** (4).
6. Verifique se o gateway de rede virtual VIP (5) do Azure Resource Manager é definido com precisão na instância do **objeto de Ligação** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Ligue uma rede virtual clássica a uma rede virtual do Gestor de Recursos

Pode criar uma ligação entre redes virtuais que estão em diferentes subscrições e em diferentes regiões. Também pode ligar redes virtuais que já têm ligações a redes no local, desde que tenha configurado o tipo de gateway como baseado em rotas.

Para configurar uma ligação entre uma rede virtual clássica e uma rede virtual Resource Manager, consulte [ligar redes virtuais de diferentes modelos de implementação utilizando o portal Azure](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![Diagrama que mostra uma ligação virtual clássica à rede virtual Azure Resource Manager.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Para verificar a configuração quando ligar uma rede virtual clássica a uma rede virtual Azure Resource Manager, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtual nestes passos correspondem aos números do diagrama anterior. 

1. Certifique-se de que não existem espaços de endereço sobreposto nas redes virtuais ligadas.
2. Verifique se o intervalo de endereços da rede virtual Azure Resource Manager (6) está definido com precisão na definição clássica da rede local (3).
3. Verifique se a gama de endereços da rede virtual clássica (1) é definida com precisão na instância do **objeto de ligação** do gestor de recursos Azure (4).
4. Verifique se o gateway de rede virtual clássico VIP (2) é definido com precisão na instância do **objeto de conexão** Azure Resource Manager (4).
5. Verifique se o gateway de rede virtual Azure Resource Manager (5) é definido com precisão na instância clássica de **Definição de Rede Local** (3).
6. Verifique se as teclas pré-partilhadas estão a coincidir em ambas as redes virtuais ligadas:
   - Rede virtual clássica: **Definição de Rede Local** (3)
   - Rede virtual Azure Resource Manager: **Objeto de ligação** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Criar uma ligação VPN ponto a site

Uma configuração ponto-a-local *(P2S* no diagrama seguinte) permite criar uma ligação segura de um computador cliente individual para uma rede virtual. As ligações ponto-a-local são úteis quando pretende ligar-se à sua rede virtual a partir de um local remoto, como a partir de casa ou de uma conferência. Também são úteis quando se tem apenas alguns clientes que precisam de se ligar a uma rede virtual. 

A ligação VPN ponto-a-local é iniciada a partir do computador cliente através do cliente nativo do Windows VPN. Os clientes de ligação utilizam certificados para autenticar.

![Ligação Site a Site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

As ligações ponto-a-local não requerem um dispositivo VPN. Criam a ligação VPN sobre o Protocolo de Túnel da Tomada Segura (SSTP). Pode ligar uma ligação ponto-a-local a uma rede virtual utilizando várias ferramentas de implantação e modelos de implementação:

* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o portal Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o portal Azure (clássico)](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Valide a sua ligação ponto-a-local

O artigo [Resolução de problemas: Problemas de ligação ponto-a-local azure](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) atravessam problemas comuns com ligações ponto-a-local.

## <a name="create-a-multisite-vpn-connection"></a>Criar uma ligação VPN multissociária

Pode adicionar uma ligação site-to-site *(S2S* no diagrama seguinte) a uma rede virtual que já tem uma ligação site-a-local, ligação ponto-a-local ou ligação rede-a-rede. Este tipo de conexão é muitas vezes chamado de configuração *multisite.* 

![Conexão multissociária](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

O Azure funciona atualmente com dois modelos de implementação: Resource Manager e clássica. Os dois modelos não são completamente compatíveis um com o outro. Para configurar uma ligação multissocimento com diferentes modelos, consulte os seguintes artigos:

* [Adicione uma ligação site-a-local a uma rede virtual com uma ligação de gateway VPN existente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Adicione uma ligação site-a-local a uma rede virtual com uma ligação de gateway VPN existente (clássico)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> Os passos nesses artigos não se aplicam ao Azure ExpressRoute e às configurações de conexão convivónica local. Para obter mais informações, consulte [o ExpressRoute e as ligações convivência site-to-site.](../expressroute/expressroute-howto-coexist-resource-manager.md)

## <a name="configure-transit-routing"></a>Configure o encaminhamento de trânsito

O encaminhamento de trânsito é um cenário específico de encaminhamento onde se conectam várias redes numa topologia da cadeia margarida. Este encaminhamento permite que os recursos em redes virtuais em cada extremidade da cadeia se comuniquem entre si através de redes virtuais pelo meio. Sem o encaminhamento de trânsito, redes ou dispositivos espreitados através de um hub não podem alcançar-se uns aos outros.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configure o encaminhamento de trânsito numa ligação ponto-a-local

Imagine um cenário em que pretende configurar uma ligação VPN site-to-site entre VNetA e VNetB. Também pretende configurar uma VPN ponto-a-local para que o cliente se conecte à porta de entrada da VNetA. Em seguida, pretende permitir que o encaminhamento de trânsito para os clientes ponto-a-local se conectem ao VNetB, que passa através do VNetA. 

Este cenário é suportado quando o BGP está ativado na VPN site-to-site entre VNetA e VNetB. Para obter mais informações, consulte [o encaminhamento VPN ponto a local.](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configure o encaminhamento de trânsito numa ligação ExpressRoute

O Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligação aos serviços cloud da Microsoft, como o Microsoft Azure, o Microsoft 365 e o Dynamics 365. Para obter mais informações, veja [Descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

![Conexão de espreitamento privado ExpressRoute às redes virtuais Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Recomendamos que, se o VNetA e o VNetB estiverem na mesma região geopolítica, [ligue ambas as redes virtuais ao circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md) em vez de configurar o encaminhamento de trânsito. Se as suas redes virtuais estiverem em diferentes regiões geopolíticas, também pode ligá-las diretamente ao seu circuito se tiver [o ExpressRoute Premium.](../expressroute/expressroute-faqs.md#expressroute-premium) 

Se tiver ExpressRoute e convivência local, o encaminhamento de trânsito não é suportado. Para obter mais informações, consulte [o Configure ExpressRoute e site-to-site utilizando o PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md).

Se tiver ativado o ExpressRoute para ligar as suas redes locais a uma rede virtual Azure, pode ativar o seu espreitamento entre as redes virtuais onde pretende ter o encaminhamento de trânsito. Para permitir que as suas redes locais se conectem à rede virtual remota, tem de configurar [o espreitamento da rede virtual.](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 

> [!Note]
> O espreitamento de rede virtual está disponível apenas para redes virtuais na mesma região.

Para verificar se configura o encaminhamento de trânsito para o estoiro da rede virtual, siga estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. [Crie um espreitamento entre VNetA e VNetB,](./tutorial-connect-virtual-networks-portal.md) como mostrado no diagrama anterior. 
3. No painel que aparece para a rede virtual, **selecione Peerings** na secção **Definições.**
4. Selecione o que deseja visualizar. Em seguida, selecione **Configuração** para validar que ativou o Permitir o **Trânsito de Gateway** na rede VNetA ligado ao circuito ExpressRoute e use o Gateway **Remoto** na rede VNetB remota não ligada ao circuito ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configurar o encaminhamento de trânsito numa ligação de observação de rede virtual

Quando as redes virtuais estão em modo de peering, pode, igualmente, configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Para configurar uma rota de trânsito em rede virtual, consulte [as ligações Rede-rede](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!Note]
> O trânsito gateway não é suportado na relação de observação entre redes virtuais criadas através de diferentes modelos de implementação. Ambas as redes virtuais na relação de observação devem ter sido criadas através do Gestor de Recursos para o trânsito de gateway para o trabalho.

Para verificar se configura uma rota de trânsito para o espreguitamento da rede virtual, siga estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém o texto **Procurar recursos** no topo do portal, digite **redes virtuais.** Quando **as redes virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Na lâmina de **redes Virtuais** que aparece, selecione a rede virtual para a qual pretende verificar a definição de espreitar.
4. No painel que aparece para a rede virtual que selecionou, **selecione Peerings** na secção **Definições.**
5. Selecione o espreitamento que pretende visualizar. Valide que ativou **Permitir o trânsito de gateway** e utilizar **gateways remotos** em **configuração**.

![Seleções para verificar se configuraram uma rota de trânsito para o espremiamento da rede virtual](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configure o encaminhamento de trânsito numa ligação rede-rede

Para configurar o encaminhamento de trânsito entre redes virtuais, deve ativar o BGP em todas as ligações rede-rede intermédias utilizando o modelo de implementação do Gestor de Recursos e o PowerShell. Para obter instruções, consulte [Como configurar o BGP nas portas Azure VPN utilizando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

O tráfego de trânsito através dos gateways Azure VPN é possível através do modelo de implementação clássico, mas que se baseia em espaços de endereços estáticos definidos no ficheiro de configuração da rede. O BGP ainda não é suportado com redes virtuais Azure e gateways VPN através do modelo de implementação clássico. Sem BGP, definir manualmente os espaços de endereço de trânsito é propenso a erros, e não o recomendamos.

> [!Note]
> Configura ligações clássicas de rede para rede utilizando o portal clássico Azure, ou utilizando um ficheiro de configuração de rede no portal clássico. Não é possível criar ou modificar uma rede virtual clássica através do modelo de implementação do Azure Resource Manager ou do portal Azure. Para obter mais informações sobre o encaminhamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configure o encaminhamento de trânsito numa ligação local-local

Para configurar o encaminhamento de trânsito entre a sua rede no local e uma rede virtual com uma ligação site-to-site, deve ativar o BGP em todas as ligações locais intermédias, utilizando o modelo de implementação do Gestor de Recursos e o PowerShell. Veja [como configurar o BGP nas portas Azure VPN utilizando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para obter instruções.

O tráfego de trânsito através dos gateways Azure VPN é possível através do modelo de implementação clássico, mas que se baseia em espaços de endereços estáticos definidos no ficheiro de configuração da rede. O BGP ainda não é suportado com redes virtuais Azure e gateways VPN através do modelo de implementação clássico. Sem BGP, definir manualmente os espaços de endereço de trânsito é propenso a erros, e não o recomendamos.

> [!Note]
> Configura ligações clássicas site-to-site utilizando o portal clássico Azure, ou usando um ficheiro de configuração de rede no portal clássico. Não é possível criar ou modificar uma rede virtual clássica através do modelo de implementação do Azure Resource Manager ou do portal Azure. Para obter mais informações sobre o encaminhamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurar o BGP para um gateway de VPN

BGP é o protocolo de encaminhamento padrão utilizado na internet para trocar informações de encaminhamento e alcance entre duas ou mais redes. Quando o BGP é utilizado no contexto das redes virtuais Azure, permite os gateways Azure VPN e os seus dispositivos VPN no local, conhecidos como pares BGP ou vizinhos. Trocam "rotas" que informarão ambos os gateways sobre a disponibilidade e a capacidade de acesso para que esses prefixos passem pelos gateways ou routers envolvidos. 

O BGP também pode permitir o encaminhamento de trânsito entre várias redes através da propagação de rotas que um gateway BGP aprende de um par BGP para todos os outros pares BGP. Para mais informações, consulte [a visão geral do BGP com o Gateway Azure VPN](../vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configure bGP para uma ligação VPN

Para configurar uma ligação VPN que utiliza BGP, consulte [Como configurar o BGP nos gateways Azure VPN utilizando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Ativar o BGP no gateway de rede virtual criando um número de sistema autónomo (AS) para o mesmo. Gateways básicos não suportam BGP. Para verificar o SKU do gateway, aceda à secção **de visão geral** da lâmina **VPN Gateway** no portal Azure. Se o seu SKU é **Básico,** tem de alterar o SKU (ver [Redimensionamento do gateway)](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway)para **VpnGw1**. 

Verificar o SKU causará 20 a 30 minutos de tempo de paragem. Assim que o gateway tiver o SKU correto, pode adicionar o número AS utilizando o comando [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) PowerShell. Depois de configurar o número AS, será fornecido automaticamente um IP de pares BGP para o gateway.

Deve fornecer manualmente `LocalNetworkGateway` um número AS e um endereço de pares BGP. Pode definir os `ASN` `-BgpPeeringAddress` valores utilizando o [comando New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) ou o comando [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) PowerShell. Alguns números de AS estão reservados para o Azure, e não pode usá-los como descrito em [About BGP com Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

O objeto de ligação deve ter O BGP ativado. Pode definir o `-EnableBGP` valor `$True` através de [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) ou [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection).

### <a name="validate-the-bgp-configuration"></a>Validar a configuração BGP

Para verificar se o BGP está configurado corretamente, pode executar os `get-AzureRmVirtualNetworkGateway` comandos e `get-AzureRmLocalNetworkGateway` comandos. Então vai notar a produção relacionada com bGP na `BgpSettingsText` parte. Por exemplo:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Criar uma ligação VPN ativa/ativa altamente disponível

As principais diferenças entre os gateways ativos/ativos e ativos/de espera são:

* Tem de criar duas configurações IP gateway com dois endereços IP públicos.
* Tem de definir a bandeira **EnableActiveActiveFeature.**
* O gateway SKU deve ser **VpnGw1,** **VpnGw2,** ou **VpnGw3**.

Para obter uma elevada disponibilidade para as instalações cruzadas e conectividade rede-rede, deve implementar vários gateways VPN e estabelecer múltiplas ligações paralelas entre as suas redes e o Azure. Para uma visão geral das opções de conectividade e topologia, consulte [as instalações cruzadas altamente disponíveis e a conectividade rede-rede.](../vpn-gateway/vpn-gateway-highlyavailable.md)

Para criar instalações cruzadas ativas/ativas e ligações rede-a-rede, siga as instruções em [Configure ligações S2S VPN ativas/ativas com gateways Azure VPN](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) para configurar uma porta VPN Azure em modo ativo/ativo.

> [!Note]  
> * Quando adicionar endereços à porta de rede local para o modo ativo/ativo ativado por BGP, *adicione apenas os endereços /32 dos pares BGP*. Se adicionar mais endereços, serão consideradas rotas estáticas e terão precedência sobre as rotas BGP.
> * Deve utilizar diferentes números BGP AS para as suas redes no local que estão a ligar-se ao Azure. (Se forem iguais, tem de alterar o número AS da sua rede virtual se o seu dispositivo VPN no local já utilizar a ASN para espreitar com outros vizinhos do BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Alterar um tipo de gateway Azure VPN após a implementação

Não é possível alterar um tipo de gateway de rede virtual Azure, baseado em políticas para baseado em rotas ou o contrário diretamente. Primeiro tem de apagar o portal. Depois disso, o endereço IP e a chave pré-partilhada não serão preservados. Então pode criar uma nova porta de entrada do tipo desejado. 

Para eliminar e criar uma porta de entrada, siga estes passos:

1. Elimine quaisquer ligações associadas ao gateway original.
2. Elimine o gateway utilizando o portal Azure, PowerShell ou PowerShell clássico: 
   * [Elimine um gateway de rede virtual utilizando o portal Azure](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Elimine uma porta de entrada de rede virtual utilizando o PowerShell](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Elimine um gateway de rede virtual utilizando o PowerShell (clássico)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Siga os passos na [Criação da porta de entrada VPN](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) para criar a nova porta de entrada do tipo pretendido e completar a configuração VPN.

> [!Note]
> Este processo levará cerca de 60 minutos.

## <a name="next-steps"></a>Passos seguintes

* [Troubleshooting connectivity problems between Azure VMs](./virtual-network-troubleshoot-connectivity-problem-between-vms.md) (Resolver problemas de conectividade entre VMs do Azure)
