---
title: 'Criar um gateway de VPN do Azure baseado em rota: PowerShell | Microsoft Docs'
description: Criar rapidamente um gateway de VPN baseado em rota usando o PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: 2a1c34ea25c8ef4bdc3e75e18d6f4a603efc67d5
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953837"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Criar um gateway de VPN baseado em rota usando o PowerShell

Este artigo ajuda você a criar rapidamente um gateway de VPN do Azure baseado em rota usando o PowerShell. Um gateway de VPN é usado ao criar uma conexão VPN com sua rede local. Você também pode usar um gateway de VPN para conectar o VNets.

As etapas neste artigo criarão uma VNet, uma sub-rede, uma sub-rede de gateway e um gateway de VPN baseado em rota (gateway de rede virtual). Depois que a criação do gateway for concluída, você poderá criar conexões. Essas etapas exigem uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada **VNet1** na localização **lesteus** :

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Crie uma configuração de sub-rede usando o cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Defina a configuração de sub-rede para a rede virtual usando o cmdlet [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="gwsubnet"></a>Adicionar uma sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que os serviços de gateway de rede virtual usam. Use os exemplos a seguir para adicionar uma sub-rede de gateway:

Defina uma variável para sua VNet.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Crie a sub-rede de gateway usando o cmdlet [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) .

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Defina a configuração de sub-rede para a rede virtual usando o cmdlet [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="PublicIP"></a>Solicitar um endereço IP público

Um gateway de VPN deve ter um endereço IP público alocado dinamicamente. Quando você cria uma conexão com um gateway de VPN, esse é o endereço IP que você especifica. Use o exemplo a seguir para solicitar um endereço IP público:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Criar a configuração de endereço IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do seu gateway:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Criar o gateway de VPN

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado. Depois que o gateway for concluído, você poderá criar uma conexão entre sua rede virtual e outra VNet. Ou crie uma conexão entre sua rede virtual e um local. Crie um gateway de VPN usando o cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) .

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Exibir o gateway de VPN

Você pode exibir o gateway de VPN usando o cmdlet [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway) .

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

A saída será semelhante a este exemplo:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Exibir o endereço IP público

Para exibir o endereço IP público para seu gateway de VPN, use o cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress) .

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

Na resposta de exemplo, o valor IpAddress é o endereço IP público.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos que criou, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para excluir o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Passos seguintes

Depois que o gateway terminar de criar, você poderá criar uma conexão entre sua rede virtual e outra VNet. Ou crie uma conexão entre sua rede virtual e um local.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma conexão com outra VNet](vpn-gateway-vnet-vnet-rm-ps.md)
