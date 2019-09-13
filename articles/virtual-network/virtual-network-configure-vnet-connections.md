---
title: Configurar e validar ligações VNet ou VPN
description: Diretrizes passo a passo para configurar e validar várias implantações de VPN e VNet do Azure
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
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901896"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>Configurar e validar ligações VNet ou VPN

Este guia passo a passo fornece orientações passo a passo para configurar e validar várias implantações de VPN e VNet do Azure em cenários como o roteamento de trânsito, VNet a VNet, BGP, multissite, ponto a site e assim por diante.

Os gateways de VPN do Azure habilitam a flexibilidade na organização de praticamente qualquer tipo de topologia de rede virtual conectada (VNet) no Azure: você pode conectar VNets entre regiões, entre tipos de VNet (Azure Resource Manager vs. Clássico), no Azure ou com o ambiente híbrido local, em assinaturas diferentes e assim por diante. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Cenário 1: Conexão VPN VNet a VNet

Conectar uma rede virtual a outra rede virtual (VNet a VNet) via VPN é semelhante a conectar uma VNet a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando **IPSec/IKE**. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes.

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Figura 1 – VNet a VNet com IPsec

Se seus VNets estiverem na mesma região, talvez você queira considerar conectá-los usando o emparelhamento VNet, que não usa um gateway de VPN, aumenta a taxa de transferência e diminui a latência, selecione **configurar e validar o emparelhamento vnet** para configurar um emparelhamento vnet conexão.

Se seus VNets forem criados usando o modelo de implantação do Azure Resource Manager, selecione **configurar e validar uma vnet do Resource Manager para uma conexão vnet do Gerenciador de recursos** para configurar uma conexão VPN.

Se um dos VNets for criado usando o modelo de implantação clássico do Azure, o outro será criado pelo Resource Manager, selecione **configurar e validar uma vnet clássica para uma conexão vnet do Gerenciador de recursos** para configurar uma conexão VPN.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Configuração 1: Configurar o emparelhamento VNet para dois VNets na mesma região

Antes de iniciar a implementação do emparelhamento VNet do Azure, certifique-se de atender aos seguintes pré-requisitos para configurar o emparelhamento VNet:

* As redes virtuais no modo de peering têm de estar na mesma região do Azure.
* As redes virtuais emparelhadas devem ter espaços de endereço IP não sobrepostos.
* O peering de rede virtual é feito entre duas redes virtuais. Não há nenhuma relação transitiva derivada entre emparelhamentos. Por exemplo, se a VNetA e a VNetB estiverem no mesmo peer e esta última e a VNetC também, a VNetA e a VNetC *não* estão no mesmo peer.

Ao atender aos requisitos, você pode seguir [um tutorial criar um emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) para criar e configurar o emparelhamento VNet.

Para verificar a configuração de emparelhamento VNet, use os seguintes métodos:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessárias.
2. Na caixa que contém os recursos de *pesquisa* de texto na parte superior da portal do Azure, digite *redes virtuais*. Quando **redes virtuais** aparecerem nos resultados da pesquisa, clique nela.
3. Na folha **redes virtuais** que aparece, clique na rede virtual para a qual você deseja criar um emparelhamento.
4. No painel que aparece para a rede virtual selecionada, clique em **emparelhamentos** na seção **configurações** .
5. Clique no emparelhamento que você deseja verificar a configuração.

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Usando o Azure PowerShell, execute o comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) para obter o emparelhamento de rede virtual, por exemplo:

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

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Tipo de conexão 1: Conectar uma VNet do Resource Manager à VNet do outro Resource Manager (Azure Resource Manager a Azure Resource Manager)

Você pode configurar uma conexão de uma VNet do Resource Manager para outra VNet do Resource Manager diretamente ou configurar a conexão com o IPsec.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Configuração 2: Configurar a conexão VPN entre o Gerenciador de recursos VNets

Para configurar uma conexão entre o Gerenciador de recursos VNets sem IPsec, consulte [Configurar uma conexão de gateway de VPN de vnet para vnet usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Para configurar uma conexão com o IPsec entre dois VNets do Resource Manager, siga as etapas 1-5 em [criar uma conexão site a site no portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) para cada VNet.

> [!Note]
> Estes passos só funcionam em VNets na mesma subscrição. Se as VNets estiverem em diferentes subscrições, tem de utilizar o PowerShell para estabelecer a ligação. Consulte o artigo [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Validar a conexão VPN entre o Gerenciador de recursos VNets

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Figura 4-conexão VNet clássica para Azure Resource Manager VNet

Para verificar se a conexão VPN está configurada corretamente, siga as instruções:

> [!Note]
> O número após os componentes de rede virtual, como "vnet 1" nas etapas abaixo, corresponde aos números da Figura 4.

1. Verifique se há espaços de endereço sobrepostos no VNets conectado.

   > [!Note]
   > Não é possível sobreposição de espaços de endereço na vnet 1 e na vnet 6. 

2. Verifique se Azure Resource Manager intervalo de endereços vnet 1 está definido com precisão no **objeto de conexão** 4.
3. Verifique se Azure Resource Manager intervalo de endereços vnet 6 está definido com precisão no **objeto de conexão** 3.
4. Verifique se as chaves pré-compartilhadas estão combinando nos dois objetos de conexão 3 e 4.
5. Verifique se a Azure Resource Manager VIP do gateway de vnet 2 está definido com precisão no **objeto de conexão** 4.
6. Verifique se a Azure Resource Manager VIP do gateway de vnet 5 está definida com precisão no **objeto de conexão** 3.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Tipo de conexão 2: Ligar uma rede virtual clássica a uma rede virtual do Resource Manager

Você pode criar uma conexão entre VNets que estão em assinaturas diferentes e em regiões diferentes. Você também pode conectar VNets que já têm conexões com redes locais, desde que você tenha configurado o tipo de gateway como baseado em rota.

Para configurar uma conexão entre uma VNet clássica e uma VNet do Resource Manager, consulte [conectar redes virtuais de diferentes modelos de implantação usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) para obter mais informações.

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Figura 5-conexão VNet clássica para Azure Resource Manager VNet

Para verificar a configuração ao conectar uma VNet clássica a uma VNet Azure Resource Manager, siga as instruções:

> [!Note]
> O número após componentes de rede virtual como "vnet 1" nas etapas abaixo corresponde aos números na Figura 5.

1. Verifique se há espaços de endereço sobrepostos no VNets conectado.

   > [!Note]
   > Não é possível sobreposição de espaços de endereço na vnet 1 e na vnet 6

2. Verifique se Azure Resource Manager intervalo de endereços VNet 6 está definido com precisão na definição de rede local clássica 3.
3. Verifique se o intervalo de endereços da VNet 1 clássico está definido com precisão no **objeto de conexão** Azure Resource Manager 4.
4. Verifique se o VIP do gateway de VNet 2 clássico está definido com precisão no **objeto de conexão** Azure Resource Manager 4.
5. Verifique se a Azure Resource Manager VIP do gateway de VNet 5 está definida com precisão na **definição de rede local** clássica 3.
6. Verifique se as chaves pré-compartilhadas são correspondentes em ambos os VNets conectados:
   1. Vnet clássica-definição de rede local 3
   2. Azure Resource Manager vnet-objeto de conexão 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Cenário 2: Conexão VPN ponto a site

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. As ligações Ponto a Site são úteis quando quer ligar a VNet a partir de uma localização remota, por exemplo, quando está em casa ou numa conferência ou quando tem apenas alguns clientes que precisam de se ligar a uma rede virtual. A ligação VPN de P2S é iniciada no computador cliente com o cliente VPN do Windows nativo. Os clientes de ligação utilizam certificados para autenticar.

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Figura 2-conexão ponto a site

As conexões ponto a site não exigem um dispositivo VPN. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol). Você pode conectar uma conexão ponto a site a uma VNet usando ferramentas de implantação e modelos de implantação diferentes:

* [Configurar uma ligação de Ponto a Site a uma VNet com o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurar uma conexão ponto a site com uma VNet usando o portal do Azure (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Validar suas conexões ponto a site

O artigo [solução de problemas: Os problemas](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) de conexão de ponto a site do Azure orientam os problemas comuns com conexões ponto a site.

## <a name="scenario-3-multi-site-vpn-connection"></a>Cenário 3: Conexão VPN de vários sites

Você pode adicionar uma conexão S2S (site a site) a uma VNet que já tem uma conexão S2S, uma conexão ponto a site ou uma conexão VNet a VNet, esse tipo de conexão é frequentemente conhecido como uma configuração **multissite** . 

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Figura 3-conexão multissite

Atualmente, o Azure funciona com dois modelos de implantação: Resource Manager e clássico. Os dois modelos não são totalmente compatíveis entre si. Para configurar a conexão **multissite** com modelos diferentes, verifique os seguintes artigos:

* [Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente (clássica)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Essas etapas não se aplicam às configurações de conexão coexistentes do ExpressRoute e site a site. Para obter mais informações sobre conexões coexistentes, consulte [conexões coexistentes do ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Cenário 4: Configurar roteamento de trânsito

O roteamento transitivo é um cenário de roteamento específico em que você conecta várias redes em uma topologia de "cadeia de Margarida". Esse roteamento permite que os recursos em Vnets em ambas as extremidades da "cadeia" se comuniquem entre si por meio de VNets. Sem roteamento transitivo, redes ou dispositivos emparelhados por meio de um Hub não podem alcançar um ao outro.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Configuração 1: Configurar o roteamento de trânsito em uma conexão ponto a site

Nesse cenário, você configura uma conexão VPN site a site entre VNetA e VNetB, também configura uma VPN ponto a site para que o cliente se conecte ao gateway do VNetA. Em seguida, você deseja habilitar o roteamento de trânsito para os clientes ponto a site se conectarem ao VNetB, que passa pelo VNetA. Esse cenário tem suporte quando o BGP está habilitado na VPN site a site entre VNetA e VNetB. Para obter mais informações, consulte [sobre o roteamento de VPN ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Configuração 2: Configurar o roteamento de trânsito em uma conexão do ExpressRoute

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.  Para obter mais informações, consulte [visão geral do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![IMAGEM](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Figura 6-conexão de "emparelhamento privado" do ExpressRoute para o Azure VNets

> [!Note]
> É recomendável que se VNetA e VNetB estiverem na mesma região de geopolítica em que você [vincular ambos VNets ao circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) em vez de configurar o roteamento transitivo. Se seus VNets estiverem em regiões geopolítica diferentes, você também poderá vinculá-los ao seu circuito diretamente se tiver o [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se você tiver a coexistência de ExpressRoute e site a site, o roteamento de trânsito não terá suporte. Para obter mais informações, consulte [Configurar conexões coexistentes de ExpressRoute e site a site para obter mais informações](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Se você tiver habilitado o ExpressRoute para conectar suas redes locais a uma rede virtual do Azure, poderá habilitar o emparelhamento VNet entre o VNets que deseja ter roteamento transitivo. Para permitir que suas redes locais se conectem à VNet remota, você deve configurar o [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> O emparelhamento VNet está disponível somente para VNets na mesma região.

Para verificar se você configurou a rota de trânsito para o emparelhamento VNet, siga as instruções:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessárias.
2. [Crie um emparelhamento entre a VNet a e B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) como no diagrama anterior (Figura 6). 
3. No painel que aparece para a rede virtual que você selecionou, clique em **emparelhamentos** na seção **configurações** .
4. Clique no emparelhamento que você deseja exibir e **configuração** para validar que você habilitou **permitir o trânsito do gateway** no VNetA conectado ao circuito do Expressroute e **use o gateway remoto** no VNetB remoto não conectado ao ExpressRoute elétrico.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Configuração 3: Configurar o roteamento de trânsito em uma conexão de emparelhamento VNet

Quando as redes virtuais estão em modo de peering, pode, igualmente, configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Para configurar a rota de trânsito no emparelhamento VNet, verifique [as conexões de rede virtual para redes virtuais](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> O tráfego de gateway não tem suporte na relação de emparelhamento entre redes virtuais criadas por meio de modelos de implantação diferentes. Para que o trânsito do gateway funcione, é necessário que ambas as redes virtuais na relação de peering tenham sido criadas com o Resource Manager.

Para verificar se você configurou a rota de trânsito para o emparelhamento VNet, siga as instruções:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)necessárias.
2. Na caixa que contém os recursos de pesquisa de texto na parte superior da portal do Azure, digite *redes virtuais*. Quando **redes virtuais** aparecerem nos resultados da pesquisa, clique nela.
3. Na folha **redes virtuais** que aparece, clique na rede virtual na qual você deseja verificar a configuração de emparelhamento.
4. No painel que aparece para a rede virtual que você selecionou, clique em **emparelhamentos** na seção **configurações** .
5. Clique no emparelhamento que você deseja exibir e valide que você habilitou **Permitir trânsito de gateway** e **use o gateway remoto** em **configuração**.

![IMAGEM](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Configuração 4: Configurar o roteamento de trânsito em uma conexão de VNet para VNet

Para configurar o roteamento de trânsito entre VNets, você deve habilitar o BGP em todas as conexões de VNet para VNet intermediárias usando o modelo de implantação do Gerenciador de recursos e o PowerShell. Para obter instruções, consulte [como configurar o BGP em gateways de VPN do Azure usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

O tráfego de trânsito por meio do gateway de VPN do Azure é possível usando o modelo de implantação clássico, mas se baseia em espaços de endereço estaticamente definidos no arquivo de configuração de rede. O BGP ainda não tem suporte com redes virtuais do Azure e gateways de VPN usando o modelo de implantação clássico. Sem o BGP, definir manualmente espaços de endereço de trânsito é propenso a erros e não é recomendado.

> [!Note]
> As conexões de VNet para VNet clássicas são configuradas usando o portal do Azure (clássico) ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica por meio do modelo de implantação Azure Resource Manager ou portal do Azure. Para obter mais informações sobre roteamento de trânsito para VNets clássicas, consulte [Hub & spoke, correntes e topologias de VNET de malha completa no Azure ARM usando VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Configuração 5: Configurar o roteamento de trânsito em uma conexão site a site

Para configurar o roteamento de trânsito entre sua rede local e uma VNet com uma conexão site a site, você deve habilitar o BGP em todas as conexões intermediárias site a site usando o modelo de implantação do Gerenciador de recursos e o PowerShell, consulte [como configurar BGP em gateways de VPN do Azure usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) para obter as instruções.

O tráfego de trânsito por meio do gateway de VPN do Azure é possível usando o modelo de implantação clássico, mas se baseia em espaços de endereço estaticamente definidos no arquivo de configuração de rede. O BGP ainda não tem suporte com redes virtuais do Azure e gateways de VPN usando o modelo de implantação clássico. Sem o BGP, definir manualmente espaços de endereço de trânsito é propenso a erros e não é recomendado.

> [!Note]
> As conexões de site a site clássicas são configuradas usando o portal do Azure (clássico) ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica por meio do modelo de implantação Azure Resource Manager ou portal do Azure. Para obter mais informações sobre roteamento de trânsito para VNets clássicas, consulte [Hub & spoke, correntes e topologias de VNET de malha completa no Azure ARM usando VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Cenário 5: Configurar o BGP para um gateway de VPN

O BGP é o protocolo de roteamento padrão usado na Internet para trocar informações de roteamento e acessibilidade entre duas ou mais redes. Quando o BGP é usado no contexto de redes virtuais do Azure, o BGP habilita os gateways de VPN do Azure e seus dispositivos VPN locais, conhecidos como vizinhos ou pares de BGP. Eles trocam "rotas" que informarão os dois gateways sobre a disponibilidade e a acessibilidade para que esses prefixos passem pelos gateways ou roteadores envolvidos. O BGP também pode permitir o encaminhamento de tráfego entre várias redes ao propagar rotas para todos os outros elementos de rede BGP, que um gateway BGP aprende de um elemento de rede BGP. Para obter mais informações, consulte [visão geral do BGP com gateways de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurar o BGP para uma conexão VPN

Para configurar uma conexão VPN que usa o BGP, consulte [como configurar o BGP em gateways de VPN do Azure usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Habilite o BGP no gateway de rede virtual criando um número AS para ele. Os gateways básicos não dão suporte a BGP. Para verificar a SKU do gateway, vá para a seção visão geral da folha gateway de VPN no portal do Azure. Se sua SKU for **básica**, você precisará alterar a SKU ([redimensionando o gateway](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) para o SKU do **VpnGw1** . A verificação da SKU causará um tempo de inatividade de 20-30 minutos. Assim que o gateway tiver o SKU correto, o as poderá ser adicionado por meio do comando [set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) do PowerShell. Depois de configurar o número as, um IP de par de BGP para o gateway será fornecido automaticamente.
> 2. O LocalNetworkGateway deve ser fornecido **manualmente** com um número as e um endereço de par BGP. Você pode definir os valores de **ASN** e **-BgpPeeringAddress** usando o comando do PowerShell [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) ou [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) . Alguns números AS são reservados para o Azure e você não pode usá-los conforme descrito em [sobre o BGP com o gateway de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Por fim, o objeto de conexão deve ter o BGP habilitado. Você pode definir o valor **-EnableBGP** como `$True` por meio [de New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) ou [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Validar a configuração de BGP

Para verificar se o BGP está configurado corretamente, você pode executar o `get-AzureRmVirtualNetworkGateway` cmdlet `get-AzureRmLocalNetworkGateway`e, em seguida, observará a saída relacionada ao BGP na parte BgpSettingsText. Por exemplo: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Cenário 6: Conexão VPN ativo-ativo altamente disponível

As principais diferenças entre os gateways ativo-ativo e ativo-em espera:

* Você deve criar duas configurações de IP de gateway com dois endereços IP públicos
* Você deve definir o sinalizador *EnableActiveActiveFeature*
* A SKU do gateway deve ser VpnGw1, VpnGw2, VpnGw3

Para obter alta disponibilidade para conectividade entre locais e VNet a VNet, você deve implantar vários gateways de VPN e estabelecer várias conexões paralelas entre suas redes e o Azure. Para obter uma visão geral das opções de conectividade e topologia, consulte [conectividade altamente disponível entre locais e vnet para vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Para criar conexões entre locais e VNet a VNet ativas, siga as instruções em [Configurar conexões VPN S2S ativas-ativas com gateways de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) para configurar o gateway de VPN do Azure no modo ativo/ativo.

> [!Note]  
> 1. Quando você adiciona endereços ao gateway de rede local para BGP habilitado, o modo ativo-para-ativo *adiciona somente os endereços/32 dos pares BGP*. Se você adicionar mais endereços, eles serão considerados rotas estáticas e terão precedência sobre rotas BGP.
> 2. Você deve usar ASNs BGP diferentes para suas redes locais conectando-se ao Azure. (Se forem os mesmos, você precisará alterar seu ASN de VNet se o dispositivo VPN local já usa o ASN para emparelhar com outros vizinhos de BGP.)

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Cenário 7: Alterar um tipo de gateway de VPN do Azure após a implantação

Você não pode alterar um tipo de gateway de VNet do Azure de baseado em política para baseado em rota ou de outra maneira diretamente. Você deve excluir o gateway, depois que o endereço IP e a PSK (chave pré-compartilhada) won'tbe preservados. Em seguida, você pode criar um novo gateway do tipo desejado. Para excluir e criar um gateway, siga as etapas:

1. Exclua todas as conexões associadas ao gateway original.
2. Exclua o gateway usando portal do Azure, PowerShell ou PowerShell clássico: 
   * [Excluir um gateway de rede virtual usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Excluir um gateway de rede virtual usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Excluir um gateway de rede virtual usando o PowerShell (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Siga as etapas em [criar o gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) para criar o novo gateway do tipo desejado e conclua a configuração de VPN.

> [!Note]
> Esse processo levará cerca de 60 minutos.

## <a name="next-steps"></a>Passos Seguintes

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms) (Resolver problemas de conectividade entre VMs do Azure)

