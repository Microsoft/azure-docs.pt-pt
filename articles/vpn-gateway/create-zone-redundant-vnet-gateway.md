---
title: Criar um gateway de rede virtual com redundância de zona em zonas de disponibilidade do Azure | Documentos da Microsoft
description: Implementar gateways do ExpressRoute e Gateway de VPN em zonas de disponibilidade
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 00c50f95324e18f738740a9bec572ca676ab9165
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010605"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Criar um gateway de rede virtual com redundância de zona em zonas de disponibilidade do Azure

Pode implementar gateways VPN e ExpressRoute em zonas de disponibilidade do Azure. Isso leva a maior disponibilidade, escalabilidade e resiliência a gateways de rede virtual. Implementar gateways em zonas de disponibilidade do Azure, física e logicamente separa gateways dentro de uma região, ao proteger a conectividade da rede no local para o Azure contra falhas de nível de zona. Para obter informações, consulte [sobre os gateways de rede virtual com redundância de zona](about-zone-redundant-vnet-gateways.md) e [sobre o Azure Availability Zones](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode utilizar o PowerShell instalado localmente no seu computador ou o Azure Cloud Shell. Se optar por instalar e utilizar o PowerShell localmente, esta funcionalidade requer a versão mais recente do módulo do PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Para utilizar o PowerShell localmente

Se estiver a utilizar o PowerShell localmente no seu computador, em vez de utilizar o Cloud Shell, tem de instalar o módulo de PowerShell 1.0.0 ou superior. Para verificar a versão do PowerShell que tenha instalado, utilize o seguinte comando:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Declarar as variáveis

Os valores utilizados para obter os passos de exemplo estão listados abaixo. Além disso, alguns dos exemplos utilizam variáveis declaradas dentro os passos. Se estiver a utilizar estes passos em seu próprio ambiente, certifique-se de que substitua estes valores pelos seus próprios. Ao especificar a localização, certifique-se de que a região a que especificar é suportada. Para obter mais informações, consulte a [FAQ](#faq).

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

## <a name="configure"></a>2. Criar a rede virtual

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

## <a name="gwsub"></a>3. Adicionar a sub-rede do gateway

A sub-rede do gateway contém os endereços IP reservados que utilizam os serviços do gateway de rede virtual. Utilize os exemplos seguintes para adicionar e defina uma sub-rede de gateway:

Adicione a sub-rede do gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Defina a configuração de sub-rede de gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Solicitar um endereço IP público
 
Neste passo, escolha as instruções que se aplicam ao gateway que pretende criar. A seleção das zonas para implementar os gateways depende as zonas especificadas para o endereço IP público.

### <a name="ipzoneredundant"></a>Para gateways com redundância de zona

Peça um endereço IP público com um **padrão** PublicIpaddress SKU e não especificar qualquer zona. Neste caso, o endereço IP público Standard criado será um IP público com redundância de zona.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Para gateways zonais

Peça um endereço IP público com um **padrão** PublicIpaddress SKU. Especifica a zona (1, 2 ou 3). Todas as instâncias de gateway serão implementadas nesta zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Para gateways regionais

Peça um endereço IP público com um **básica** PublicIpaddress SKU. Neste caso, o gateway é implementado como um gateway regional e não tem nenhuma redundância de zona, criada para o gateway. As instâncias de gateway são criadas em qualquer zonas, respectivamente.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Criar a configuração de IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Criar o gateway

Crie o gateway de rede virtual.

### <a name="for-expressroute"></a>Para o ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Para o Gateway VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que será alterado quando eu implantar esses novos SKUs?

Do ponto de vista, pode implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implementadas em zonas de disponibilidade do Azure, e cada zona de disponibilidade é um domínio de falha e de atualização diferente. Isso faz com que os gateways mais confiável, disponível e resiliente a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Pode utilizar o portal do Azure?

Sim, pode utilizar o portal do Azure para implementar os novos SKU. No entanto, verá esses novos SKUs apenas nas regiões do Azure têm zonas de disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para mim utilizar os novos SKU?

Ver [zonas de disponibilidade](../availability-zones/az-overview.md#regions-that-support-availability-zones) para a lista mais recente das regiões disponíveis.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualização meus gateways de rede virtual existente para gateways com redundância de zona ou zonais?

Migrar os seus gateways de rede virtual existente para gateways com redundância de zona ou zonais não é atualmente suportada. No entanto, pode eliminar o gateway existente e voltar a criar um gateway com redundância de zona ou zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Pode implementar gateways VPN e o Express Route na mesma rede virtual?

Coexistência de gateways VPN e o Express Route na mesma rede virtual é suportada. No entanto, deve reservar/27 intervalo de endereços IP para a sub-rede do gateway.
