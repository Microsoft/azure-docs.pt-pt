---
title: Criar um gateway de rede virtual com redundância de zona no Zonas de Disponibilidade do Azure
description: Implantar gateway de VPN e gateways de ExpressRoute no Zonas de Disponibilidade
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/26/2019
ms.author: cherylmc
ms.openlocfilehash: 250ced13696d6ec34e7c434b26a2917a3c55e91d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834638"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Criar um gateway de rede virtual com redundância de zona no Zonas de Disponibilidade do Azure

Você pode implantar gateways VPN e ExpressRoute no Zonas de Disponibilidade do Azure. Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas. Para obter informações, consulte [sobre gateways de rede virtual com redundância de zona](about-zone-redundant-vnet-gateways.md) e [sobre zonas de disponibilidade do Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode usar o PowerShell instalado localmente em seu computador ou o Azure Cloud Shell. Se você optar por instalar e usar o PowerShell localmente, esse recurso exigirá a versão mais recente do módulo do PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Para usar o PowerShell localmente

Se você estiver usando o PowerShell localmente em seu computador, em vez de usar Cloud Shell, deverá instalar o módulo do PowerShell 1.0.0 ou superior. Para verificar a versão do PowerShell que você instalou, use o seguinte comando:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. declare suas variáveis

Os valores usados para as etapas de exemplo são listados abaixo. Além disso, alguns dos exemplos usam variáveis declaradas dentro das etapas. Se você estiver usando essas etapas em seu próprio ambiente, certifique-se de substituir esses valores pelos seus próprios. Ao especificar o local, verifique se há suporte para a região especificada. Para obter mais informações, consulte as [perguntas frequentes](#faq).

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

## <a name="configure"></a>2. criar a rede virtual

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

## <a name="gwsub"></a>3. adicionar a sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que os serviços de gateway de rede virtual usam. Use os exemplos a seguir para adicionar e definir uma sub-rede de gateway:

Adicione a sub-rede do gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Defina a configuração de sub-rede de gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. solicitar um endereço IP público
 
Nesta etapa, escolha as instruções que se aplicam ao gateway que você deseja criar. A seleção de zonas para implantar os gateways depende das zonas especificadas para o endereço IP público.

### <a name="ipzoneredundant"></a>Para gateways com redundância de zona

Solicite um endereço IP público com um SKU PublicIpaddress **padrão** e não especifique nenhuma zona. Nesse caso, o endereço IP público padrão criado será um IP público com redundância de zona.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Para gateways zonais

Solicite um endereço IP público com um SKU PublicIpaddress **padrão** . Especifique a zona (1, 2 ou 3). Todas as instâncias de gateway serão implantadas nessa zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Para gateways regionais

Solicite um endereço IP público com um SKU PublicIpaddress **básico** . Nesse caso, o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona embutida no gateway. As instâncias de gateway são criadas em qualquer zona, respectivamente.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. criar a configuração de IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. criar o gateway

Crie o gateway de rede virtual.

### <a name="for-expressroute"></a>Para ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Para o gateway de VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que será alterado quando eu implantar essas novas SKUs?

Da sua perspectiva, você pode implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implantadas em Zonas de Disponibilidade do Azure, e cada zona de disponibilidade será um domínio de atualização e de falha diferente. Isso torna seus gateways mais confiáveis, disponíveis e resilientes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal do Azure?

Sim, você pode usar o portal do Azure para implantar as novas SKUs. No entanto, você verá esses novos SKUs somente nas regiões do Azure que têm Zonas de Disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Quais regiões estão disponíveis para usar as novas SKUs?

Consulte [zonas de disponibilidade](../availability-zones/az-overview.md#services-support-by-region) para obter a lista mais recente de regiões disponíveis.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar meus gateways de rede virtual existentes para gateways com redundância de zona ou zonas.

No momento, não há suporte para migrar seus gateways de rede virtual existentes para gateways com redundância de zona ou zonas. Você pode, no entanto, excluir o gateway existente e recriar um gateway com redundância de zona ou zonas.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar os gateways de VPN e de rota expressa na mesma rede virtual?

Há suporte para a coexistência de gateways de VPN e de rota expressa na mesma rede virtual. No entanto, você deve reservar um intervalo de endereços IP/27 para a sub-rede de gateway.
