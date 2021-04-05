---
title: 'Configure ExpressRoute e ligações coexistindo S2S VPN: clássico'
description: Este artigo explica-lhe como configurar o ExpressRoute e uma ligação de Rede de VPNs que pode coexistir para o modelo de implementação clássica.
documentationcenter: na
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: 70407d726b697dd6c4dfb6777cf6c445092d3b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206330"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Configurar ligações coexistentes do ExpressRoute e de Site a Site (clássico)
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Clássico](expressroute-howto-coexist-classic.md)
> 
> 

Este artigo ajuda-o a configurar as ligações ExpressRoute e Site-to-Site VPN que coexistem. A capacidade de configurar o ExpressRoute e a Rede de VPNs tem várias vantagens. Pode configurar a VPN site-to-site como um caminho de falha seguro para ExpressRoute, ou utilizar VPNs site-to-site para ligar a sites que não estão conectados através do ExpressRoute. Abordaremos os passos para configurar ambos os cenários neste artigo. Este artigo aplica-se ao modelo de implementação clássica. Esta configuração não está disponível no portal.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Os circuitos ExpressRoute têm de ser previamente configurados antes de seguir as instruções abaixo. Confirme que seguiu os guias para [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) e [configurar o encaminhamento](expressroute-howto-routing-classic.md) antes de seguir os passos abaixo.
> 
> 

## <a name="limits-and-limitations"></a>Limites e limitações
* **Encaminhamento de tráfego não suportado.** Não pode encaminhar (através do Azure) entre a sua rede local ligada através da Rede de VPNs e a sua rede local ligada através do ExpressRoute.
* **Ligação ponto a site não suportada.** Não pode ativar ligações VPN ponto a site para a mesma VNet que está ligada ao ExpressRoute. VPN ponto a site e ExpressRoute não podem coexistir na mesma VNet.
* **Não pode ativar o túnel forçado no Gateway de Rede de VPNs.** Pode apenas “forçar” todo o tráfego da Internet a voltar à sua rede no local através do ExpressRoute.
* **Gateway do SKU Básico não suportado.** Tem de utilizar um gateway do SKU não Básico para o [Gateway do ExpressRoute](expressroute-about-virtual-network-gateways.md) e para o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **É apenas suportado o Gateway de VPN baseado na rota.** Tem de utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) baseado na rota.
* **A rota estática deve ser configurada para o seu Gateway de VPN.** Se a sua rede local estiver ligada ao ExpressRoute e a uma Rede de VPNs, terá de ter uma rota estática configurada na rede local para encaminhar a ligação de Rede de VPNs para a Internet pública.
* **O gateway do ExpressRoute tem de ser configurado primeiro.** Tem de criar primeiro o Gateway do ExpressRoute antes de adicionar o Gateway de Rede de VPNs.

## <a name="configuration-designs"></a>Estruturas de configuração
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma Rede de VPNs como um caminho de ativação pós-falha para o ExpressRoute
Pode configurar uma ligação de Rede de VPNs como uma cópia de segurança para o ExpressRoute. Isto aplica-se apenas às redes virtuais ligadas ao caminho de peering privado do Azure. Não existe qualquer solução de ativação pós-falha baseada em VPN para os serviços acessíveis através dos peerings público do Azure e da Microsoft. O circuito ExpressRoute é sempre a ligação primária. Os dados percorrerão o caminho da Rede de VPNs apenas se o circuito ExpressRoute falhar. 

> [!NOTE]
> Apesar de o circuito do ExpressRoute ser preferível face à Rede de VPNs quando ambas as rotas são as mesmas, o Azure irá utilizar a correspondência de prefixo mais longo para escolher a rota de acordo com o destino do pacote.
> 
> 

![Diagrama que mostra uma ligação VPN site-to-site como uma cópia de segurança para ExpressRoute.](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma Rede de VPNs para se ligar a sites não ligados através do ExpressRoute
Pode configurar a sua rede para um local no qual alguns sites se ligam diretamente ao Azure através da Rede de VPNs e alguns sites estabelecem ligação através do ExpressRoute. 

![Coexistir](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Não pode configurar uma rede virtual como um router de tráfego.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selecionar os passos a utilizar
Existem dois conjuntos diferentes de procedimentos à sua escolha para configurar ligações que podem coexistir. O procedimento de configuração que selecionou dependerá do facto de pretender ligar-se a uma rede virtual já existente ou criar uma nova rede virtual.

* Não tenho uma VNet e preciso de criar uma.
  
    Se ainda não tem uma rede virtual, este procedimento irá orientá-lo durante a criação de uma nova rede virtual com o modelo de implementação clássica e de novas ligações de Rede de VPNs e ExpressRoute. Para configurar, siga os passos na secção do artigo [Para criar uma nova rede virtual e ligações coexistentes](#new).
* Já tenho um modelo de implementação clássica VNet.
  
    Pode já ter uma rede virtual no local com uma ligação ExpressRoute ou de Rede de VPNs existente. A secção de [artigos Para configurar ligações coexistindo para um VNet já existente](#add) irá acompanhá-lo através da eliminação do gateway, e, em seguida, criar novas ligações ExpressRoute e VPN site-to-site. Tenha em atenção que, quando criar novas ligações, os passos têm de ser concluídos por uma ordem muito específica. Não utilize as instruções de outros artigos para criar os seus gateways e ligações.
  
    Neste procedimento, a criação de ligações coexistentes implicará eliminar o seu gateway e, em seguida, configurar os novos gateways. Isto significa que terá período de inatividade para as suas ligações entre locais enquanto elimina e recria o seu gateway e as suas ligações, mas não terá de migrar qualquer uma das suas VMs ou serviços para uma nova rede virtual. As VMs e os serviços continuarão a poder comunicar através do balanceador de carga enquanto configura o seu gateway, se estiverem configurados para tal.

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Para criar uma nova rede virtual e ligações coexistentes
Este procedimento irá explicar-lhe como criar uma VNet e criar ligações coexistentes ExpressRoute e de Rede de VPNs.

1. Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para obter mais informações sobre como instalar os cmdlets PowerShell. Tenha em atenção que os cmdlets que irá utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções. 
2. Crie um esquema para a sua rede virtual. Para obter mais informações sobre a configuração do esquema, veja [Esquema de configuração da Virtual Network do Azure](/previous-versions/azure/reference/jj157100(v=azure.100)).
   
    Quando cria o seu esquema, confirme que utiliza os seguintes valores:
   
   * A sub-rede do gateway para a rede virtual tem de ser /27 ou ter um prefixo mais curto (como /26 ou /25).
   * O gateway tem um tipo de ligação “Dedicado”.
     
    ```xml
    <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
      <AddressSpace>
        <AddressPrefix>10.17.159.192/26</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="Subnet-1">
          <AddressPrefix>10.17.159.192/27</AddressPrefix>
        </Subnet>
        <Subnet name="GatewaySubnet">
          <AddressPrefix>10.17.159.224/27</AddressPrefix>
          /Subnet>
      </Subnets>
      <Gateway>
        <ConnectionsToLocalNetwork>
          <LocalNetworkSiteRef name="MyLocalNetwork">
            <Connection type="Dedicated" />
          </LocalNetworkSiteRef>
        </ConnectionsToLocalNetwork>
      </Gateway>
    </VirtualNetworkSite>
    ```
3. Depois de criar e configurar o seu ficheiro de esquema xml, carregue o ficheiro. Tal irá criar a sua rede virtual.
   
    Utilize o seguinte cmdlet para carregar o seu ficheiro, substituindo o valor com o seu próprio.
   
    ```azurepowershell
    Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
    ```
4. <a name="gw"></a>Crie um Gateway do ExpressRoute. Verifique se especifica o GatewaySKU como *Standard*, *HighPerformance* ou *UltraPerformance* e o GatewayType como *DynamicRouting*.
   
    Utilize o exemplo seguinte, substituindo os valores pelos seus próprios.

    ```azurepowershell
    New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
    ```
5. Ligue o gateway ExpressRoute ao circuito ExpressRoute. Quando tiver concluído este passo, a ligação entre a sua rede no local e do Azure, através do ExpressRoute, é estabelecida.
   
    ```azurepowershell
    New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
    ```
6. <a name="vpngw"></a>Em seguida, crie o gateway de Rede de VPNs. O GatewaySKU tem de ser *Standard*, *HighPerformance* ou *UltraPerformance* e o GatewayType tem de ser *DynamicRouting*.
   
    ```azurepowershell
    New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
    ```
   
    Para obter as definições do gateway de rede virtual, incluindo o ID do gateway e o IP público, utilize o cmdlet `Get-AzureVirtualNetworkGateway`.
   
    ```azurepowershell
    Get-AzureVirtualNetworkGateway
   
    GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
    GatewayName          : S2SVPN
    LastEventData        :
    GatewayType          : DynamicRouting
    LastEventTimeStamp   : 5/29/2015 4:41:41 PM
    LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
    LastEventID          : 23002
    State                : Provisioned
    VIPAddress           : 104.43.x.y
    DefaultSite          :
    GatewaySKU           : HighPerformance
    Location             :
    VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
    SubnetId             :
    EnableBgp            : False
    OperationDescription : Get-AzureVirtualNetworkGateway
    OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
    OperationStatus      : Succeeded
    ```
7. Crie uma entidade de gateway de VPN de site local. Este comando não configurar o seu gateway de VPN no local. Em vez disso, este permite-lhe fornecer as definições do gateway local, tal como o IP público e o espaço de endereço no local, para que o gateway de VPN do Azure se possa ligar a este.
   
   > [!IMPORTANT]
   > O site local para a VPN de Site a Site não está definido no netcfg. Em vez disso, tem de utilizar este cmdlet para especificar os parâmetros do site local. Não pode defini-lo, nem através do portal nem do ficheiro netcfg.
   > 
   > 
   
    Utilize o exemplo seguinte, substituindo os valores com os seus próprios.
   
    ```azurepowershell
    New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
    ```
   
   > [!NOTE]
   > Se a sua rede local tiver várias rotas, pode passá-las a todas como uma matriz.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Para obter as definições do gateway de rede virtual, incluindo o ID do gateway e o IP público, utilize o cmdlet `Get-AzureVirtualNetworkGateway`. Veja o seguinte exemplo.

    ```azurepowershell
    Get-AzureLocalNetworkGateway

    GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
    GatewayName          : MyLocalNetwork
    IpAddress            : 23.39.x.y
    AddressSpace         : {10.1.2.0/24}
    OperationDescription : Get-AzureLocalNetworkGateway
    OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
    OperationStatus      : Succeeded
    ```


1. Configure o seu dispositivo VPN local para estabelecer ligação com o novo gateway. Utilize as informações que obteve no passo 6 quando configurar o seu dispositivo VPN. Para obter mais informações sobre a configuração do dispositivo VPN, veja [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Ligue o gateway de Rede de VPNs no Azure ao gateway local.
   
    Neste exemplo, o connectedEntityId é o ID local do gateway, o qual pode encontrar executando `Get-AzureLocalNetworkGateway`. Pode encontrar o virtualNetworkGatewayId com o cmdlet `Get-AzureVirtualNetworkGateway`. Após este passo, é estabelecida a ligação entre a rede local e o Azure através da ligação VPN Site a Site.

    ```azurepowershell
    New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Para configurar ligações coexistentes a uma VNet já existente
Se tiver uma rede virtual existente, verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway é /28 ou /29, tem primeiro de eliminar o gateway da rede virtual e aumentar o tamanho da sub-rede do gateway. Os passos nesta secção mostrarão como o fazer.

Se a sub-rede do gateway é /27 ou superior e a rede virtual está ligada através do ExpressRoute, pode ignorar os passos abaixo e avançar para [“Passo 6 – Criar um gateway de Rede de VPNs”](#vpngw) na secção anterior.

> [!NOTE]
> Ao eliminar o gateway existente, o local irá perder a ligação à sua rede virtual enquanto estiver a trabalhar nesta configuração.
> 
> 

1. Terá de instalar a versão mais recente dos cmdlets PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para obter mais informações sobre como instalar os cmdlets PowerShell. Tenha em atenção que os cmdlets que irá utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções. 
2. Elimine o gateway ExpressRoute ou de Rede de VPNs existente. Utilize o cmdlet seguinte, substituindo os valores com os seus próprios.
   
    ```azurepowershell
    Remove-AzureVNetGateway –VnetName MyAzureVNET
    ```
3. Exporte o esquema de rede virtual. Utilize o cmdlet PowerShell seguinte, substituindo os valores com os seus próprios.
   
    ```azurepowershell
    Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
    ```
4. Edite o esquema do ficheiro de configuração de rede para que a sub-rede do gateway seja /27 ou um prefixo mais curto (como /26 ou /25). Veja o seguinte exemplo. 
   
   > [!NOTE]
   > Se não tem endereços IP suficientes na sua rede virtual para aumentar o tamanho da sub-rede do gateway, tem de adicionar mais espaço de endereços IP. Para obter mais informações sobre a configuração do esquema, veja [Esquema de configuração da Virtual Network do Azure](/previous-versions/azure/reference/jj157100(v=azure.100)).
   > 
   > 
   
    ```xml
    <Subnet name="GatewaySubnet">
      <AddressPrefix>10.17.159.224/27</AddressPrefix>
    </Subnet>
    ```
5. Se o seu gateway anterior foi uma VPN de Site a Site, também tem de alterar o tipo de ligação para **Dedicado**.
   
    ```xml
    <Gateway>
      <ConnectionsToLocalNetwork>
        <LocalNetworkSiteRef name="MyLocalNetwork">
          <Connection type="Dedicated" />
        </LocalNetworkSiteRef>
      </ConnectionsToLocalNetwork>
    </Gateway>
    ```
6. Neste momento, terá uma VNet sem quaisquer gateways. Para criar gateways novos e concluir as suas ligações, pode continuar com [Passo 4 – Criar um gateway ExpressRoute](#gw), presente no conjunto de passos anterior.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md)