---
title: Configure e valide ligações de rede virtual ou VPN
description: Orientação passo a passo para configurar e validar várias vpn azure e implementações de redes virtuais
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
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099063"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configure e valide ligações de rede virtual ou VPN

Este walkthrough fornece orientação passo a passo para configurar e validar várias VPN Azure e implementações de rede virtual. Os cenários incluem o encaminhamento de trânsito, ligações de rede para rede, Protocolo border gateway (BGP), ligações multilocais e ligações ponto-a-local.

Os gateways Azure VPN permitem flexibilidade na organização de quase qualquer tipo de topologia de rede virtual conectada em Azure. Por exemplo, pode ligar redes virtuais:

- Através de regiões.
- Entre tipos de rede virtuais (Azure Resource Manager versus classic).
- Dentro do Azure ou dentro de um ambiente híbrido no local.
- Em diferentes assinaturas. 

## <a name="network-to-network-vpn-connection"></a>Ligação VPN de rede-rede

Ligar uma rede virtual a outra rede virtual (rede-rede) via VPN é semelhante à ligação de uma rede virtual a uma localização no local no local. Ambos os tipos de conectividade usam um gateway VPN para fornecer um túnel seguro através de IPsec e IKE. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes.

![Ligação rede-rede com IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se as suas redes virtuais estiverem na mesma região, é melhor considerar ligá-las utilizando o peering virtual da rede. O epeering virtual da rede não usa um gateway VPN. Aumenta a entrada e diminui a latência. Para configurar uma ligação de peering de rede virtual, **selecione Configurar e validar o Peering VNet**.

Se as suas redes virtuais foram criadas através do modelo de implementação de Dispositivos Azure, **selecione Configure e valide um VNet do Gestor de Recursos para uma ligação VNet do Gestor** de Recursos para configurar uma ligação VPN.

Se uma das redes virtuais foi criada através do modelo de implantação clássico do Azure, e a outra foi criada através do Gestor de Recursos, **selecione Configure e valide uma vNet clássica a uma ligação VNet de Gestor de Recursos** para configurar uma ligação VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configure o peering da rede virtual para duas redes virtuais na mesma região

Antes de começar a implementar e configurar o peering da rede virtual Azure, certifique-se de que cumpre os seguintes pré-requisitos:

* As redes virtuais no modo de peering têm de estar na mesma região do Azure.
* As redes virtuais espreitadas devem ter espaços de endereço IP que não se sobrepõem.
* O peering de rede virtual é feito entre duas redes virtuais. Não há uma relação transitiva derivada entre os pares. Por exemplo, se a VNetA for espreitada com VNetB, e vNetB for espreitada com VNetC, vNetA *não* é espreitada com VNetC.

Quando cumprir os requisitos, pode seguir o [Tutorial: Conectar redes virtuais com o peering de rede virtual utilizando o portal Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) para criar e configurar o peering.

Para verificar a configuração do peering, utilize o seguinte método:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém os recursos de **pesquisa** de texto no topo do portal, digite **redes virtuais.** Quando **as redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Na lâmina de **redes Virtuais** que aparece, selecione a rede virtual para a qual pretende criar um epeering.
4. No painel que aparece para a rede virtual, selecione **Peerings** na secção **Definições.**
5. Selecione um olhar e veja os resultados da configuração.

![Seleções para verificar a configuração de peering de rede virtual](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Para o Azure PowerShell, execute o comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) para obter o peering da rede virtual. Segue-se um exemplo:

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

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configure uma ligação VPN entre redes virtuais do Gestor de Recursos

Para configurar uma ligação entre redes virtuais do Gestor de Recursos sem IPsec, consulte [configurar uma ligação vpn de rede para rede utilizando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Para configurar uma ligação com o IPsec entre duas redes virtuais do Gestor de Recursos, siga os passos 1 a 5 em [Criar uma ligação site-a-site no portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) para cada rede virtual.

> [!Note]
> Estes passos funcionam apenas para redes virtuais na mesma subscrição. Se as suas redes virtuais estiverem em subscrições diferentes, tem de utilizar o PowerShell para efazer a ligação. Consulte o artigo [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Validar a ligação VPN entre redes virtuais do Gestor de Recursos

![Conexão clássica de rede virtual a uma rede virtual do Gestor de Recursos Azure](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Para verificar se a sua ligação VPN está corretamente configurada, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtuais nestes passos correspondem aos números do diagrama anterior.

1. Certifique-se de que não existem espaços de endereços sobrepostos nas redes virtuais conectadas.
2. Verifique se a gama de endereços da rede virtual do Gestor de Recursos Azure (1) é definida com precisão na instância do **objeto de ligação** (4).
3. Verifique se a gama de endereços da rede virtual do Gestor de Recursos Azure (6) é definida com precisão na instância do **objeto de ligação** (3).
4. Verifique se as teclas pré-partilhadas estão a condizer com os objetos de ligação.
5. Verifique se o gateway de rede virtual DO Gestor de Recursos Azure VIP (2) é definido com precisão na instância do **objeto de ligação** (4).
6. Verifique se o gateway de rede virtual DO Gestor de Recursos Azure VIP (5) é definido com precisão na instância do **objeto de ligação** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Ligue uma rede virtual clássica a uma rede virtual do Gestor de Recursos

Pode criar uma ligação entre redes virtuais que estão em diferentes subscrições e em diferentes regiões. Também pode ligar redes virtuais que já têm ligações a redes no local, desde que tenha configurado o tipo de gateway como baseado em rotas.

Para configurar uma ligação entre uma rede virtual clássica e uma rede virtual do Gestor de Recursos, consulte [redes virtuais Connect de diferentes modelos de implementação utilizando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Conexão clássica de rede virtual a uma rede virtual do Gestor de Recursos Azure](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Para verificar a configuração quando ligar uma rede virtual clássica a uma rede virtual do Gestor de Recursos Azure, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtuais nestes passos correspondem aos números do diagrama anterior. 

1. Certifique-se de que não existem espaços de endereços sobrepostos nas redes virtuais conectadas.
2. Verifique se a gama de endereços da rede virtual Do Gestor de Recursos Azure (6) é definida com precisão na definição clássica de rede local (3).
3. Verifique se a gama de endereços para a rede virtual clássica (1) é definida com precisão na instância de objeto seletiva de **ligação** ao gestor de recursos do Azure (4).
4. Verifique se o gateway de rede virtual clássico VIP (2) é definido com precisão na instância de objeto de **ligação** do Gestor de Recursos Azure (4).
5. Verifique se o portal de rede virtual do Gestor de Recursos Azure (5) é definido com precisão na clássica instância de **Definição** de Rede Local (3).
6. Verifique se as teclas pré-partilhadas estão a condizer em ambas as redes virtuais conectadas:
   - Rede virtual clássica: **Definição** de Rede Local (3)
   - Rede virtual do Gestor de Recursos Azure: Objeto de **ligação** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Criar uma ligação VPN ponto-a-local

Uma configuração ponto-a-local *(P2S* no diagrama seguinte) permite criar uma ligação segura de um computador cliente individual para uma rede virtual. As ligações ponto-a-local são úteis quando pretende ligar-se à sua rede virtual a partir de um local remoto, como em casa ou numa conferência. Também são úteis quando se tem apenas alguns clientes que precisam de se conectar a uma rede virtual. 

A ligação VPN ponto-a-site é iniciada a partir do computador cliente através do cliente VPN nativo do Windows. Os clientes de ligação utilizam certificados para autenticar.

![Ligação Site a Site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

As ligações ponto-a-local não requerem um dispositivo VPN. Criam a ligação VPN sobre o Protocolo de Túnel de Tomada Segura (SSTP). Pode ligar uma ligação ponto-a-local a uma rede virtual utilizando várias ferramentas de implementação e modelos de implementação:

* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o portal Azure (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Validar a sua ligação ponto-a-local

O artigo Resolução de [problemas: Os problemas de ligação ponto-a-local do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) passam por questões comuns com ligações ponto-a-local.

## <a name="create-a-multisite-vpn-connection"></a>Criar uma ligação VPN multilocal

Pode adicionar uma ligação site-a-site *(S2S* no diagrama seguinte) a uma rede virtual que já tem uma ligação site-a-site, ligação ponto-a-local ou ligação rede-a-rede. Este tipo de ligação é muitas vezes chamada de configuração *multilocal.* 

![Conexão multilocal](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

O Azure funciona atualmente com dois modelos de implementação: Resource Manager e clássica. Os dois modelos não são completamente compatíveis um com o outro. Para configurar uma ligação multi-local com diferentes modelos, consulte os seguintes artigos:

* [Adicione uma ligação site-a-site a uma rede virtual com uma conexão de gateway VPN existente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Adicione uma ligação site-a-site a uma rede virtual com uma conexão de gateway VPN existente (clássica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Os passos nesses artigos não se aplicam às configurações de ligação do Azure ExpressRoute e do site-a-site. Para mais informações, consulte [expressRoute e ligações coexistentes site-to-site](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Configure o encaminhamento de trânsito

O encaminhamento de trânsito é um cenário específico de encaminhamento onde se conectam várias redes numa topologia da cadeia de margaridas. Este encaminhamento permite que os recursos em redes virtuais em cada extremidade da cadeia se comuniquem entre si através de redes virtuais pelo meio. Sem o encaminhamento de trânsito, redes ou dispositivos espreitados através de um hub não podem alcançar uns aos outros.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configure o encaminhamento de trânsito numa ligação ponto-a-local

Imagine um cenário em que pretende configurar uma ligação VPN site-to-site entre VNetA e VNetB. Também pretende configurar uma VPN ponto-a-local para o cliente se ligar à porta de entrada da VNetA. Em seguida, pretende permitir o encaminhamento de trânsito para que os clientes ponto-a-site se conectem ao VNetB, que passa pela VNetA. 

Este cenário é suportado quando o BGP está ativado no site-to-site VPN entre VNetA e VNetB. Para mais informações, consulte [sobre o encaminhamento VPN ponto-a-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configure o encaminhamento de trânsito numa ligação ExpressRoute

O Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365. Para obter mais informações, veja [Descrição geral do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Ligação privada expressRoute às redes virtuais azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Recomendamos que se vNetA e VNetB estiverem na mesma região geopolítica, você [liga ambas as redes virtuais ao circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) em vez de configurar o encaminhamento de trânsito. Se as suas redes virtuais estiverem em diferentes regiões geopolíticas, também pode ligá-las diretamente ao seu circuito se tiver [o ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se tiver a ExpressRoute e a coexistência local-a-local, o encaminhamento de trânsito não é suportado. Para mais informações, consulte [configure ExpressRoute e site-to-site utilizando powerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Se permitiu que o ExpressRoute ligasse as suas redes locais a uma rede virtual Azure, pode permitir o peering entre as redes virtuais onde pretende ter encaminhamento de trânsito. Para permitir que as suas redes locais se conectem à rede virtual remota, tem de configurar [o peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> O epeering de rede virtual está disponível apenas para redes virtuais na mesma região.

Para verificar se configurao encaminhamento de trânsito para o epeering virtual da rede, siga estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. [Crie um olhar entre VNetA e VNetB,](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) como mostrado no diagrama anterior. 
3. No painel que aparece para a rede virtual, selecione **Peerings** na secção **Definições.**
4. Selecione o epeering que deseja ver. Em seguida, selecione **Configuração** para validar que ativou **permitir o trânsito** de gateway na rede VNetA ligada ao circuito ExpressRoute e utilize gateway **remoto** na rede VNetB remota não ligada ao circuito ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configure o encaminhamento de trânsito numa ligação virtual de peering de rede

Quando as redes virtuais estão em modo de peering, pode, igualmente, configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Para configurar uma rota de trânsito no peering de rede virtual, consulte [as ligações Rede-a-rede](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> O trânsito gateway não é suportado na relação de pares entre redes virtuais criadas através de diferentes modelos de implementação. Ambas as redes virtuais na relação de peering devem ter sido criadas através do Gestor de Recursos para o trânsito de gateway para funcionar.

Para verificar se configurauma rota de trânsito para o epeering virtual da rede, siga estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém os recursos de **pesquisa** de texto no topo do portal, digite **redes virtuais.** Quando **as redes Virtuais** aparecerem nos resultados da pesquisa, selecione-as.
3. Na lâmina de **redes Virtuais** que aparece, selecione a rede virtual para a qual pretende verificar a definição de peering.
4. No painel que aparece para a rede virtual que selecionou, selecione **Peerings** na secção **Definições.**
5. Selecione o olhar que pretende ver. Valide que ativou **permitir o trânsito** de gateway e utilize **gateways remotos** sob **configuração**.

![Seleções para verificar se configurauma rota de trânsito para o peering de rede virtual](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configure o encaminhamento de trânsito numa ligação rede-rede

Para configurar o encaminhamento de trânsito entre redes virtuais, deve ativar o BGP em todas as ligações intermédias de rede-rede utilizando o modelo de implementação do Gestor de Recursos e o PowerShell. Para obter instruções, consulte [Como configurar bGP nos gateways Azure VPN utilizando powerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

O tráfego de trânsito através dos portões Azure VPN é possível através do modelo de implementação clássico, mas que se baseia em espaços de endereço státicamente definidos no ficheiro de configuração da rede. O BGP ainda não é suportado com redes virtuais Azure e gateways VPN através do modelo de implementação clássico. Sem BGP, definir manualmente os espaços de endereço de trânsito é propenso a erros, e não o recomendamos.

> [!Note]
> Configura as ligações clássicas de rede-rede utilizando o portal clássico Azure, ou utilizando um ficheiro de configuração de rede no portal clássico. Não é possível criar ou modificar uma rede virtual clássica através do modelo de implementação do Gestor de Recursos Azure ou do portal Azure. Para obter mais informações sobre o encaminhamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configure o encaminhamento de trânsito numa ligação site-a-local

Para configurar o encaminhamento de trânsito entre a sua rede no local e uma rede virtual com uma ligação site-to-site, deve ativar o BGP em todas as ligações intermédias do site-para-local utilizando o modelo de implementação do Gestor de Recursos e a PowerShell. Consulte [como configurar bGP nos portões Azure VPN utilizando](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) o PowerShell para obter instruções.

O tráfego de trânsito através dos portões Azure VPN é possível através do modelo de implementação clássico, mas que se baseia em espaços de endereço státicamente definidos no ficheiro de configuração da rede. O BGP ainda não é suportado com redes virtuais Azure e gateways VPN através do modelo de implementação clássico. Sem BGP, definir manualmente os espaços de endereço de trânsito é propenso a erros, e não o recomendamos.

> [!Note]
> Configura as ligações clássicas site-to-site utilizando o portal clássico Azure, ou utilizando um ficheiro de configuração de rede no portal clássico. Não é possível criar ou modificar uma rede virtual clássica através do modelo de implementação do Gestor de Recursos Azure ou do portal Azure. Para obter mais informações sobre o encaminhamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurar o BGP para um gateway de VPN

BGP é o protocolo padrão de encaminhamento usado na internet para trocar informações de encaminhamento e alcance entre duas ou mais redes. Quando o BGP é utilizado no contexto das redes virtuais Azure, permite os gateways VPN Azure e os seus dispositivos VPN no local, conhecidos como pares de BGP ou vizinhos. Trocam "rotas" que informarão tanto os gateways sobre a disponibilidade e a capacidade de acesso para que esses prefixos passem pelos gateways ou routers envolvidos. 

O BGP também pode permitir o encaminhamento de trânsito entre várias redes, propagando rotas que um gateway BGP aprende de um par de BGP para todos os outros pares do BGP. Para mais informações, consulte a [visão geral do BGP com o Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configure BGP para uma ligação VPN

Para configurar uma ligação VPN que utiliza BGP, consulte [como configurar bGP em gateways VPN Azure utilizando powerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Ative o BGP na porta de entrada da rede virtual, criando um número de sistema autónomo (AS) para o mesmo. Os portões básicos não suportam o BGP. Para verificar o SKU do portal gateway, vá à secção **de visão geral** da lâmina **VPN Gateway** no portal Azure. Se o seu SKU for **Básico,** tem de mudar o SKU (ver [Redimensionar a porta](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)de entrada) para **VpnGw1**. 

Verificar o SKU causará 20 a 30 minutos de tempo de inatividade. Assim que o portal tiver o SKU correto, pode adicionar o número AS utilizando o comando [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell. Depois de configurar o número AS, será fornecido automaticamente um IP de pares BGP para o gateway.

Deve fornecer `LocalNetworkGateway` manualmente um número AS e um endereço de pares BGP. Pode definir `ASN` os `-BgpPeeringAddress` valores e valores utilizando o [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) ou o comando [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell. Alguns números AS estão reservados para O Azure, e não pode usá-los como descrito em [Sobre BGP com Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

O objeto de ligação deve ter BGP ativado. Pode definir `-EnableBGP` o `$True` valor através do [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) ou [do Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Validar a configuração do BGP

Para verificar se o BGP está configurado `get-AzureRmVirtualNetworkGateway` corretamente, pode executar os comandos e `get-AzureRmLocalNetworkGateway` comandos. Em seguida, você vai notar a `BgpSettingsText` saída relacionada com BGP na peça. Por exemplo:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Criar uma ligação VPN ativa/ativa altamente disponível

As principais diferenças entre os gateways ativos/ativos e ativos/ativos/de espera são:

* Deve criar duas configurações IP de gateway com dois endereços IP públicos.
* Tem de definir a bandeira **EnableActiveActiveFeature.**
* O gateway SKU deve ser **VpnGw1,** **VpnGw2**, ou **VpnGw3**.

Para obter uma elevada disponibilidade para as instalações cruzadas e conectividade rede-rede, deverá implementar vários gateways VPN e estabelecer múltiplas ligações paralelas entre as suas redes e o Azure. Para uma visão geral das opções de conectividade e topologia, consulte as [instalações transversais altamente disponíveis e a conectividade rede-rede.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)

Para criar instalações cruzadas ativas/ativas e ligações de rede-rede, siga as instruções em [ligações S2S VPN ativas/ativas com gateways VPN Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) para configurar um gateway VpN Azure em modo ativo/ativo.

> [!Note]  
> * Quando adicionar endereços à porta de entrada de rede local para o modo ativo/ativo ativa ativa ativa ativa/ativa ativa ativa, *adicione apenas os endereços /32 dos pares BGP*. Se adicionar mais endereços, serão consideradas rotas estáticas e terão precedência sobre as rotas do BGP.
> * Você deve usar diferentes números De BGP AS para as suas redes no local que estão conectando-se ao Azure. (Se forem iguais, tem de alterar o número AS da sua rede virtual se o seu dispositivo VPN no local já utilizar o ASN para espreitar com outros vizinhos do BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Alterar um tipo de gateway Azure VPN após a implantação

Não é possível alterar um tipo de gateway de rede virtual Azure de baseado em políticas para baseado em rotas ou o contrário diretamente. Primeiro deve apagar o portal. Depois disso, o endereço IP e a chave pré-partilhada não serão preservados. Então pode criar uma nova porta de entrada do tipo desejado. 

Para eliminar e criar um portal, siga estes passos:

1. Elimine quaisquer ligações associadas ao gateway original.
2. Elimine o portal Azure, PowerShell ou powerShell clássico: 
   * [Eliminar um portal de rede virtual utilizando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Eliminar um portal de rede virtual utilizando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Eliminar um portal de rede virtual utilizando o PowerShell (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Siga os passos em [Criar o gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) para criar a nova porta de entrada do tipo pretendido e completar a configuração VPN.

> [!Note]
> Este processo levará cerca de 60 minutos.

## <a name="next-steps"></a>Passos seguintes

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms) (Resolver problemas de conectividade entre VMs do Azure)

