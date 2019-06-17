---
title: 'Crie um gateway de VPN do Azure baseado na rota: PowerShell | Microsoft Docs'
description: Crie rapidamente um Gateway de VPN baseado na rota com o PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3ad391aae72bec392ae75b333fc3bfc1ff3f95f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66727325"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Criar um gateway VPN baseado na rota com o PowerShell

Este artigo ajuda-o a criar rapidamente um gateway de VPN do Azure baseado na rota com o PowerShell. Um gateway de VPN é utilizado quando criar uma ligação VPN à sua rede no local. Também pode utilizar um gateway de VPN para ligar VNets.

Os passos neste artigo irão criar uma VNet, uma sub-rede, uma sub-rede de gateway e um gateway de VPN baseado na rota (gateway de rede virtual). Depois de concluída a criação de gateway, em seguida, pode criar ligações. Estes passos requerem uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Criar uma rede virtual

Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada **VNet1** no **EastUS** localização:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Criar uma configuração de sub-rede com o [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet.

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Definir a configuração de sub-rede para a rede virtual, utilizando o [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) cmdlet.


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="gwsubnet"></a>Adicionar uma sub-rede de gateway

A sub-rede do gateway contém os endereços IP reservados que utilizam os serviços do gateway de rede virtual. Utilize os exemplos seguintes para adicionar uma sub-rede de gateway:

Defina uma variável para a sua VNet.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Crie a sub-rede de gateway com o [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) cmdlet.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Definir a configuração de sub-rede para a rede virtual, utilizando o [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) cmdlet.

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="PublicIP"></a>Peça um endereço IP público

Um gateway de VPN tem de ter um endereço IP público alocado dinamicamente. Quando cria uma ligação a um gateway VPN, este é o endereço IP que especificar. Utilize o seguinte exemplo para pedir um endereço IP público:

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
## <a name="CreateGateway"></a>Criar o gateway VPN

Um gateway de VPN pode demorar 45 minutos ou mais a ser criado. Quando o gateway estiver concluído, pode criar uma ligação entre a rede virtual e a outra VNet. Em alternativa, criar uma ligação entre a rede virtual e uma localização no local. Criar um gateway de VPN com o [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Ver o gateway de VPN

Pode ver o gateway de VPN com o [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway) cmdlet.

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

O resultado será semelhante a este exemplo:

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

## <a name="viewgwpip"></a>Ver o endereço IP público

Para ver o endereço IP público para o seu gateway VPN, utilize o [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress) cmdlet.

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

Na resposta de exemplo, o valor de IpAddress é o endereço IP público.

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

Quando já não precisar dos recursos que criou, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Passos Seguintes

Depois do gateway tenha terminado de criar, pode criar uma ligação entre a rede virtual e a outra VNet. Em alternativa, criar uma ligação entre a rede virtual e uma localização no local.

> [!div class="nextstepaction"]
> [Criar uma ligação site a site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Criar uma ligação ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Criar uma ligação a outra VNet](vpn-gateway-vnet-vnet-rm-ps.md)
