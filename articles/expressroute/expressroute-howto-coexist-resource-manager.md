---
title: 'Configurar ligações de VPN de Site a Site e ExpressRoute - coexistir: PowerShell: Azure | Microsoft Docs'
description: Configure o ExpressRoute e uma ligação de VPN de Site a Site que pode coexistir para o modelo do Resource Manager com o PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 4a1f9556413df7ad8954171d2b446419d3bc2975
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092315"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Configurar ligações coexistentes ExpressRoute e de Site a Site com o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Clássica](expressroute-howto-coexist-classic.md)
> 
> 

Este artigo ajuda-o a configurar ligações VPN Site a Site e ExpressRoute que coexistem. A capacidade de configurar o ExpressRoute e a Rede de VPNs tem várias vantagens. Pode configurar VPN de Site a Site como um caminho de ativação pós-falha seguro para o ExpressRoute ou utilizar VPNs de Site a Site para ligar a sites que não estão ligados através do ExpressRoute. Abordaremos os passos para configurar ambos os cenários neste artigo. Este artigo aplica-se ao modelo de implementação clássica e do Resource Manager.

A configuração de ligações de Rede de VPNs e ExpressRoute coexistentes tem várias vantagens:

* Pode configurar uma Rede de VPNs como um caminho de ativação pós-falha para o ExpressRoute. 
* Em alternativa, pode utilizar a Rede de VPNs para se ligar a sites que não estão ligados através do ExpressRoute. 

São abrangidos neste artigo os passos para configurar ambos os cenários. Este artigo aplica-se ao modelo de implementação do Resource Manager e utiliza o PowerShell. Também pode configurar estes cenários com o portal do Azure, embora a documentação ainda não está disponível. Pode configurar um gateway pela primeira vez. Normalmente, irá incorrer em sem períodos de indisponibilidade ao adicionar um novo gateway ou a ligação de gateway.

>[!NOTE]
>Se pretender criar uma Rede de VPNs através de um circuito do ExpressRoute, veja [este artigo](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Limites e limitações
* **Encaminhamento de tráfego não suportado.** Não pode encaminhar (através do Azure) entre a sua rede local ligada através da Rede de VPNs e a sua rede local ligada através do ExpressRoute.
* **Gateway do SKU Básico não suportado.** Tem de utilizar um gateway do SKU não Básico para o [Gateway do ExpressRoute](expressroute-about-virtual-network-gateways.md) e para o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **É apenas suportado o Gateway de VPN baseado na rota.** Tem de utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) baseado na rota.
* **A rota estática deve ser configurada para o seu Gateway de VPN.** Se a sua rede local estiver ligada ao ExpressRoute e a uma Rede de VPNs, terá de ter uma rota estática configurada na rede local para encaminhar a ligação de Rede de VPNs para a Internet pública.
* **Gateway de VPN por predefinição ASN de 65515 se não for especificado.** O Gateway de VPN do Azure suporta o protocolo de encaminhamento de BGP. Pode especificar o ASN (número as) para uma rede virtual ao adicionar o comutador - Asn. Se não especificar este parâmetro, a predefinição como o número é 65515. Pode usar qualquer ASN para a configuração, mas se selecionar algo diferente de 65515, tem de repor o gateway para a definição tenha efeito.

## <a name="configuration-designs"></a>Estruturas de configuração
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma Rede de VPNs como um caminho de ativação pós-falha para o ExpressRoute
Pode configurar uma ligação de Rede de VPNs como uma cópia de segurança para o ExpressRoute. Esta ligação aplica-se apenas às redes virtuais ligadas ao caminho de peering privado do Azure. Não existe qualquer solução de ativação pós-falha baseada em VPN para os serviços acessíveis através dos peerings do Azure e da Microsoft. O circuito ExpressRoute é sempre a ligação primária. Os dados percorrem o caminho da Rede de VPNs apenas se o circuito ExpressRoute falhar. Para evitar o encaminhamento assimétrico, a sua configuração de rede local também deve preferir o circuito do ExpressRoute em vez da Rede de VPNs. Pode preferir o caminho do ExpressRoute ao definir uma preferência local mais elevada para as rotas que receberam o ExpressRoute. 

> [!NOTE]
> Apesar de o circuito do ExpressRoute ser preferível face à Rede de VPNs quando ambas as rotas são as mesmas, o Azure irá utilizar a correspondência de prefixo mais longo para escolher a rota de acordo com o destino do pacote.
> 
> 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma Rede de VPNs para se ligar a sites não ligados através do ExpressRoute
Pode configurar a sua rede para um local no qual alguns sites se ligam diretamente ao Azure através da Rede de VPNs e alguns sites estabelecem ligação através do ExpressRoute. 

![Coexistir](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Não pode configurar uma rede virtual como um router de tráfego.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selecionar os passos a utilizar
Existem dois conjuntos de procedimentos diferentes à escolha. O procedimento de configuração que selecionar depende do facto de pretender ligar-se a uma rede virtual já existente ou criar uma nova.

* Não tenho uma VNet e preciso de criar uma.
  
    Se ainda não tem uma rede virtual, este procedimento orienta-o durante a criação de uma rede virtual nova com o modelo de implementação Resource Manager e a criação de ligações de Rede de VPNs e ExpressRoute novas. Para configurar uma rede virtual, siga os passos em [Para criar uma rede virtual nova e ligações coexistentes](#new).
* Já tenho uma VNet do modelo de implementação Resource Manager.
  
    Pode já ter uma rede virtual no local com uma ligação ExpressRoute ou de Rede de VPNs existente. Neste cenário, se a máscara de sub-rede do gateway for /28 ou inferior (/28, /29, etc.), tem de eliminar o gateway existente. A secção [Para configurar ligações coexistentes a uma VNet já existente](#add) orienta-o através da eliminação do gateway e, em seguida, da criação de novas ligações ExpressRoute e de Rede de VPNs.
  
    Se eliminar e recriar o seu gateway, terá um período de inatividade para as ligações em vários locais. No entanto, as VMs e os serviços continuarão a poder comunicar através do balanceador de carga enquanto configura o seu gateway, se estiverem configurados para tal.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="new"></a>Para criar uma nova rede virtual e ligações coexistentes
Este procedimento orienta-o ao longo da criação de uma VNet e de ligações ExpressRoute e de Rede de VPNs que vão coexistir. Os cmdlets que vai utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções.

1. Inicie sessão e selecione a sua subscrição.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Definir variáveis.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Crie uma rede virtual, incluindo uma Sub-Rede do Gateway. Para obter mais informações sobre como criar uma rede virtual, veja [Criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Para obter mais informações sobre como criar sub-redes, veja [Criar uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)
   
   > [!IMPORTANT]
   > A sub-rede do Gateway tem de ser /27 ou ter um prefixo mais curto (como /26 ou /25).
   > 
   > 
   
    Crie uma nova VNet.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Adicione sub-redes.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Guarde a configuração da VNet.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Em seguida, crie o Gateway de Rede de VPNs. Para obter mais informações sobre a configuração do Gateway de VPN, veja [Configurar uma VNet com uma ligação de Rede de VPNs](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). O GatewaySku só é suportado para *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* e nos gateways de VPN *HighPerformance*. As configurações coexistentes do Gateway de VPN do ExpressRoute não são suportadas no SKU Básico. O VpnType tem de ser *RouteBased*.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    O gateway de VPN do Azure oferece suporte ao protocolo de encaminhamento BGP. Pode especificar o ASN (Número AS) para essa Rede Virtual, ao adicionar o comutador -Asn no comando seguinte. Se não especificar esse parâmetro, o padrão do número AS será 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    Pode encontrar o IP do peering de BGP e o número AS que o Azure utiliza para o Gateway de VPN em $azureVpn.BgpSettings.BgpPeeringAddress e $azureVpn.BgpSettings.Asn. Para obter mais informações, veja o artigo [Configure BGP (Configurar o BGP)](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para o Gateway de VPN do Azure.
5. Crie uma entidade de gateway de VPN de site local. Este comando não configurar o seu gateway de VPN no local. Em vez disso, este permite-lhe fornecer as definições do gateway local, tal como o IP público e o espaço de endereço no local, para que o gateway de VPN do Azure se possa ligar a este.
   
    Se o seu dispositivo VPN local suportar apenas o encaminhamento estático, pode configurar as rotas estáticas da seguinte forma:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Se o seu dispositivo VPN local suportar o BGP e se pretender ativar o encaminhamento dinâmico, terá de conhecer o IP do peering de BGP e o número AS que o seu dispositivo VPN local utiliza.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Configure o seu dispositivo VPN local para estabelecer ligação com o novo gateway de VPN do Azure. Para obter mais informações sobre a configuração do dispositivo VPN, veja [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Ligue o gateway de Rede de VPNs no Azure ao gateway local.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Se estiver a ligar a um circuito do ExpressRoute existente, ignore os passos 8 e 9 e avance para o passo 10. Configure circuitos do ExpressRoute. Para obter mais informações sobre configurar o circuito do ExpressRoute, veja [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md).


9. Configure o peering privado do Azure no circuito do ExpressRoute. Para obter mais informações sobre como configurar o peering privado do Azure no circuito do ExpressRoute, veja [Configurar peering](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>Crie um Gateway do ExpressRoute. Para obter mais informações sobre a configuração do gateway ExpressRoute, veja [Configuração do gateway ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). O GatewaySKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance*.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Ligue o gateway ExpressRoute ao circuito ExpressRoute. Quando tiver concluído este passo, a ligação entre a sua rede no local e do Azure, através do ExpressRoute, é estabelecida. Para obter mais informações sobre a operação de ligação, veja [Ligar VNets ao ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="add"></a>Para configurar ligações coexistentes a uma VNet já existente
Se tiver uma rede virtual que tenha apenas um gateway de rede virtual (digamos, gateway de VPN de Site para Site) e pretende adicionar outro gateway de um tipo diferente (digamos, gateway do ExpressRoute), verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway for /27 ou mais, pode ignorar os passos abaixo e seguir os passos na secção anterior para adicionar um gateway de VPN de Site para Site ou um gateway do ExpressRoute. Se a sub-rede do gateway for /28 ou /29, primeiro tem de eliminar o gateway de rede virtual e aumentar o tamanho da sub-rede do gateway. Os passos nesta secção mostram-lhe como o fazer.

Os cmdlets que vai utilizar para esta configuração podem ser ligeiramente diferentes do que poderá estar familiarizado. Confirme que utiliza os cmdlets especificados nestas instruções.

1. Elimine o gateway ExpressRoute ou de Rede de VPNs existente.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Elimine a sub-Rede do Gateway.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Adicione uma Sub-Rede do Gateway que é /27 ou superior.
   
   > [!NOTE]
   > Se não tem endereços IP suficientes na sua rede virtual para aumentar o tamanho da sub-rede do gateway, tem de adicionar mais espaço de endereços IP.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Guarde a configuração da VNet.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. Nesta fase, já tem uma rede virtual sem quaisquer gateways. Para criar gateways novos e configurar as ligações, siga os passos na secção anterior.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Para adicionar uma configuração ponto a site para o gateway de VPN

Pode seguir os passos abaixo para adicionar a configuração de ponto a Site para o gateway de VPN numa configuração de coexistência. Para carregar o certificado de raiz VPN, tem de instalar o PowerShell localmente para o seu computador ou utilizar o portal do Azure.

1. Adicione um conjunto de endereços de Cliente de VPN.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Carregue o certificado de raiz VPN para o Azure para o seu gateway de VPN. Neste exemplo, é assumido que o certificado de raiz é armazenado no computador local em que os seguintes cmdlets do PowerShell são executados e que está a executar localmente do PowerShell. Também pode carregar o certificado com o portal do Azure.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

Para obter mais informações sobre VPNs Ponto a Site, veja [Configurar uma ligação Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
