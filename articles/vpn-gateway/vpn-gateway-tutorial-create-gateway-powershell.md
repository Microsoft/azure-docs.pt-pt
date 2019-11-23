---
title: Tutorial - Create and manage a gateway using Azure VPN Gateway
description: Tutorial - Criar e Gerir o gateway de VPN com o módulo do Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: b144a70ee88138966d9cc38a56e1cff1e63fca1b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424139"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutorial: Create and manage a VPN gateway using PowerShell

Os gateways de VPN do Azure fornecem conectividade em vários locais entre as instalações do cliente e o Azure. Este tutorial abrange itens básicos da implementação do gateway de VPN do Azure, tais como criar e gerir um gateway de VPN. Saiba como:

> [!div class="checklist"]
> * Criar um Gateway de VPN
> * View the public IP address
> * Redimensionar um gateway de VPN
> * Repor um Gateway de VPN

O diagrama seguinte mostra a rede virtual e o gateway de VPN criados como parte deste tutorial.

![VNet e gateway de VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell e Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Valores de parâmetros de rede comuns

Below are the parameter values used for this tutorial. In the examples, the variables translate to the following:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Change the values below based on your environment and network setup, then copy and paste to set the variables for this tutorial. If your Cloud Shell session times out, or you need to use a different PowerShell window, copy and paste the variables to your new session and continue the tutorial.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Create a resource group with the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Primeiro, tem de ser criado um grupo de recursos. No exemplo seguinte, é criado um grupo de recursos com o nome *TestRG1* na região *E.U.A. Leste*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Criar rede virtual

O gateway de VPN do Azure fornece conectividade em vários locais e a funcionalidade de servidor VPN P2S para a sua rede virtual. Adicione o gateway de VPN a uma rede virtual existente ou crie uma nova rede virtual e o gateway. Notice that the example specifies the name of the gateway subnet specifically. You must always specify the name of the gateway subnet as "GatewaySubnet" in order for it to function properly. This example creates a new virtual network with three subnets: Frontend, Backend, and GatewaySubnet using [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) and [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Pedir um endereço IP público para o gateway de VPN

Os gateways de VPN do Azure comunicam com os dispositivos VPN no local através da Internet para efetuar a negociação de IKE (Internet Key Exchange) e estabelecer túneis IPsec. Create and assign a public IP address to your VPN gateway as shown in the example below with [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) and [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Atualmente, só pode utilizar um endereço IP público dinâmico para o gateway. O endereço IP estático não é suportado nos gateways de VPN do Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Criar um Gateway de VPN

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado. Depois de concluída a criação do gateway, pode criar uma ligação entre a rede virtual e outra VNet. Em alternativa, pode criar uma ligação entre a rede virtual e uma localização no local. Create a VPN gateway using the [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valores de parâmetros chave:
* GatewayType: utilize **Vpn** para ligações site a site e VNet a VNet
* VpnType: Utilize **RouteBased** para interagir com um leque mais vasto de dispositivos VPN e mais funcionalidades de encaminhamento
* GatewaySku: **VpnGw1** is the default; change it to another VpnGw SKU if you need higher throughputs or more connections. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

If you are using the TryIt, your session may time out. That's OK. The gateway will still create.

Depois de concluída a criação do gateway, pode criar uma ligação entre a rede virtual e outra VNet ou criar uma ligação entre a rede virtual e uma localização no local. Também pode configurar uma ligação P2S para a sua VNet a partir de um computador cliente.

## <a name="view-the-gateway-public-ip-address"></a>View the gateway public IP address

If you know the name of the public IP address, use [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) to show the public IP address assigned to the gateway.

If your session timed out, copy the common network parameters from the beginning of this tutorial into your new session and proceed, then proceed.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Resize a gateway

Pode alterar a SKU do gateway de VPN após a criação do gateway. Different gateway SKUs support different specifications such as throughputs, number of connections, etc. The following example uses [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) to resize your gateway from VpnGw1 to VpnGw2. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

O redimensionamento de um gateway de VPN também demora cerca de 30 a 45 minutos, embora esta operação **não** vá interromper ou remover as ligações e configurações existentes.

## <a name="reset-a-gateway"></a>Reset a gateway

Como parte dos passos de resolução de problemas, pode repor o gateway de VPN do Azure para obrigar o gateway VPN a reiniciar as configurações de túnel IPsec/IKE. Use [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) to reset your gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Para obter mais informações, veja [Repor um gateway de VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Limpar recursos

If you're advancing to the [next tutorial](vpn-gateway-tutorial-vpnconnection-powershell.md), you will want to keep these resources because they are the prerequisites.

However, if the gateway is part of a prototype, test, or proof-of-concept deployment, you can use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command to remove the resource group, the VPN gateway, and all related resources.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação e gestão básicas do gateway de VPN, como, por exemplo:

> [!div class="checklist"]
> * Criar um Gateway de VPN
> * View the public IP address
> * Redimensionar um gateway de VPN
> * Repor um Gateway de VPN

Avance para os tutoriais seguintes para saber mais sobre ligações S2S, VNet a VNet e P2S.

> [!div class="nextstepaction"]
> * [Criar ligações S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Criar ligações VNet a VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar ligações P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
