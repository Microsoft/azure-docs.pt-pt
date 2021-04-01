---
title: Criar uma porta de entrada de rede virtual redundante em Zonas de Disponibilidade Azure
description: Saiba como implementar gateways VPN e gateways ExpressRoute redundantes em Zonas de Disponibilidade Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2eaf1470e2d861ecfc1c1bc96f6040a1c3e0a644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425233"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Criar uma porta de entrada de rede virtual redundante em Zonas de Disponibilidade Azure

Pode implantar gateways VPN e ExpressRoute em Zonas de Disponibilidade Azure. Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas. Para obter informações, consulte [sobre as portas de rede virtuais redundantes de zona e](about-zone-redundant-vnet-gateways.md) sobre as zonas de disponibilidade de [Azure.](../availability-zones/az-overview.md)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Declare as suas variáveis

Declare as variáveis que quer utilizar. Utilize o exemplo seguinte, substituindo os valores pelos seus próprios, quando necessário. Se fechar a sessão PowerShell/Cloud Shell em qualquer ponto durante o exercício, basta copiar e colar novamente os valores para re declarar as variáveis. Ao especificar a localização, verifique se a região especificada é suportada. Para mais informações, consulte as [FAQ.](#faq)

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

A sub-rede gateway contém os endereços IP reservados que os serviços de gateway de rede virtuais utilizam. Utilize os seguintes exemplos para adicionar e definir uma sub-rede de gateway:

Adicione a sub-rede do gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Deslote a configuração da sub-rede gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Solicitar um endereço IP público
 
Neste passo, escolha as instruções que se aplicam ao gateway que pretende criar. A seleção das zonas de implantação dos gateways depende das zonas especificadas para o endereço IP público.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Para portais redundantes de zona

Solicite um endereço IP público com um SKU **standard** PublicIpaddress e não especifique nenhuma zona. Neste caso, o endereço IP público standard criado será um IP público redundante.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Para gateways zonais

Solicite um endereço IP público com um SKU **standard** PublicIpaddress. Especificar a zona (1, 2 ou 3). Todas as instâncias de gateway serão implantadas nesta zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Para portais regionais

Solicite um endereço IP público com um SKU de endereço público **básico.** Neste caso, a porta de entrada é implantada como uma porta de entrada regional e não tem qualquer zona-redundância incorporada na porta de entrada. As instâncias de gateway são criadas em quaisquer zonas, respectivamente.

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

Crie o portal de rede virtual.

### <a name="for-expressroute"></a>Para ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Para VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implementar estes novos SKUs?

Do seu ponto de vista, pode implantar os seus gateways com redundância de zona. Isto significa que todas as instâncias dos gateways serão implantadas em todas as Zonas de Disponibilidade de Azure, e cada Zona de Disponibilidade é um domínio de falha e atualização diferente. Isto torna os seus gateways mais fiáveis, disponíveis e resistentes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal Azure?

Sim, pode usar o portal Azure para implantar os novos SKUs. No entanto, você verá estes novos SKUs apenas nas regiões Azure que têm Azure Availability Zones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para eu utilizar os novos SKUs?

Consulte [As Zonas de Disponibilidade](../availability-zones/az-region.md) para obter a última lista de regiões disponíveis.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar os meus gateways de rede virtuais existentes para portas de entrada redundantes ou zonais?

A migração dos seus gateways de rede virtuais existentes para portas de portais redundantes ou zonais não é suportada atualmente. Pode, no entanto, apagar o seu gateway existente e recriar uma porta de entrada redundante ou zonal.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implementar os gateways VPN e Express Route na mesma rede virtual?

A coexistência de gateways VPN e Express Route na mesma rede virtual é suportada. No entanto, deverá reservar um intervalo de endereço IP /27 para a sub-rede gateway.
