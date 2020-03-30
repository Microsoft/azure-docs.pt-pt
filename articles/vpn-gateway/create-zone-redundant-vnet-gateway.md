---
title: Criar uma porta de entrada de rede virtual redundante em Zonas de Disponibilidade Azure
description: Implementar gateways VPN Gateway e ExpressRoute em Zonas de Disponibilidade
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150197"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Criar uma porta de entrada de rede virtual redundante em Zonas de Disponibilidade Azure

Pode implementar gateways VPN e ExpressRoute em Zonas de Disponibilidade Azure. Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas. Para obter informações, consulte [sobre gateways de rede virtual redundantes](about-zone-redundant-vnet-gateways.md) e sobre zonas de [disponibilidade azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Declare as suas variáveis

Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário. Se fechar a sua sessão PowerShell/Cloud Shell em qualquer ponto durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis. Ao especificar a localização, verifique se a região que especifica está apoiada. Para mais informações, consulte as [FAQ](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Criar a rede virtual

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Crie uma rede virtual

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Adicione a sub-rede gateway

A sub-rede gateway contém os endereços IP reservados que os serviços de gateway da rede virtual utilizam. Utilize os seguintes exemplos para adicionar e definir uma sub-rede de gateway:

Adicione a sub-rede do gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Detete a configuração da sub-rede de gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Solicitar um endereço IP público
 
Neste passo, escolha as instruções que se aplicam ao portal que pretende criar. A seleção de zonas para implantação dos portões depende das zonas especificadas para o endereço IP público.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Para gateways de zona redundante

Solicite um endereço IP público com um **SKU Standard** PublicIpaddress e não especifique nenhuma zona. Neste caso, o endereço IP público standard criado será um IP público redundante.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Para gateways zonais

Solicite um endereço IP público com um **SKU Standard** PublicIpaddress. Especifique a zona (1, 2 ou 3). Todas as instâncias de gateway serão implantadas nesta zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Para gateways regionais

Solicite um endereço IP público com **um** SKU Basic PublicIpaddress. Neste caso, a porta de entrada é implantada como porta de entrada regional e não tem qualquer redundância de zona incorporada na porta de entrada. As instâncias de gateway são criadas em qualquer zona, respectivamente.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Criar a configuração IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Criar o portal

Crie o portal da rede virtual.

### <a name="for-expressroute"></a>Para A Rota Expressa

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Para vpn gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implementar estas novas SKUs?

Do seu ponto de vista, pode implantar os seus portões com redundância de zona. Isto significa que todos os casos dos gateways serão implantados em zonas de disponibilidade do Azure, e cada Zona de Disponibilidade é um domínio de falha e atualização diferente. Isto torna os seus gateways mais fiáveis, disponíveis e resistentes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal Azure?

Sim, pode usar o portal Azure para implementar as novas SKUs. No entanto, você verá estas novas UsS apenas nas regiões azure que têm Zonas de Disponibilidade Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para eu usar as novas SKUs?

Consulte [as Zonas de Disponibilidade](../availability-zones/az-overview.md#services-support-by-region) para obter a mais recente lista de regiões disponíveis.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso mudar/migrar/atualizar os meus portões de rede virtual existentes para gateways de zona redundante ou zonal?

Migrar as suas portas de rede virtual existentes para gateways zonas redundantes ou zonais não é suportado atualmente. Pode, no entanto, eliminar o seu portal existente e recriar um gateway zona-redundante ou zonal.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar os gateways VPN e Express Route na mesma rede virtual?

A coexistência de gateways VPN e Express Route na mesma rede virtual é suportada. No entanto, deve reservar uma gama de endereços IP /27 para a sub-rede gateway.
