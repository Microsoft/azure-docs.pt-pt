---
title: 'Gateway de VPN do Azure: definições de configuração'
description: Saiba mais sobre as configurações de gateway de VPN para gateways de rede virtual do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 9ecea0576f7cec4e70d6e223b5412a8d8057b2ab
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894730"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre as definições de configuração do gateway de VPN

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado entre sua rede virtual e sua localização local em uma conexão pública. Você também pode usar um gateway de VPN para enviar tráfego entre redes virtuais no backbone do Azure.

Uma conexão de gateway de VPN depende da configuração de vários recursos, cada um deles contém configurações configuráveis. As seções neste artigo abordam os recursos e as configurações relacionados a um gateway de VPN para uma rede virtual criada no modelo de implantação do Gerenciador de recursos. Você pode encontrar descrições e diagramas de topologia para cada solução de conexão no artigo [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md) .

Os valores neste artigo aplicam gateways de VPN (gateways de rede virtual que usam o-Gatewaytype VPN). Este artigo não abrange todos os tipos de gateway ou gateways com redundância de zona.

* Para valores que se aplicam a-Gatewaytype ' ExpressRoute ', consulte [gateways de rede virtual para ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Para gateways com redundância de zona, consulte [sobre gateways com redundância de zona](about-zone-redundant-vnet-gateways.md).

* Para a WAN virtual, consulte [sobre a WAN virtual](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual pode ter apenas um gateway de rede virtual de cada tipo. Ao criar um gateway de rede virtual, você deve verificar se o tipo de gateway está correto para sua configuração.

Os valores disponíveis para-Gatewaytype são:

* Vpn
* ExpressRoute

Um gateway de VPN requer a *VPN*`-GatewayType`.

Exemplo:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Configurar um SKU de gateway

#### <a name="azure-portal"></a>Portal do Azure

Se você usar o portal do Azure para criar um gateway de rede virtual do Resource Manager, poderá selecionar o SKU do gateway usando a lista suspensa. As opções que você recebe correspondem ao tipo de gateway e ao tipo de VPN que você selecionar.

#### <a name="powershell"></a>PowerShell

O exemplo do PowerShell a seguir especifica o `-GatewaySku` como VpnGw1. Ao usar o PowerShell para criar um gateway, primeiro você precisa criar a configuração de IP e, em seguida, usar uma variável para fazer referência a ela. Neste exemplo, a variável de configuração é $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Redimensionando ou alterando uma SKU

Se você tiver um gateway de VPN e quiser usar um SKU de gateway diferente, suas opções serão redimensionar a SKU do gateway ou para alterar para outra SKU. Quando você altera para outro SKU de gateway, você exclui completamente o gateway existente e cria um novo. Um gateway pode levar até 45 minutos para ser criado. Em comparação, quando você redimensiona uma SKU de gateway, não há muito tempo de inatividade porque você não precisa excluir e recompilar o gateway. Se você tiver a opção de redimensionar o SKU do gateway, em vez de alterá-lo, você desejará fazer isso. No entanto, há regras sobre o redimensionamento:

1. Com exceção da SKU básica, você pode redimensionar um SKU de gateway de VPN para outro SKU de gateway de VPN dentro da mesma geração (Generation1 ou Generation2). Por exemplo, VpnGw1 de Generation1 pode ser redimensionado para VpnGw2 de Generation1, mas não para VpnGw2 de Generation2.
2. Ao trabalhar com os antigos SKU de gateway, pode redimensionar entre SKU Básicos, Standard e de alto desempenho.
3. **Não é possível** redimensionar de SKUs Basic/Standard/HighPerformance para SKUs VpnGw. Em vez disso, você deve [alterar](#change) para as novas SKUs.

#### <a name="resizegwsku"></a>Para redimensionar um gateway

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Para alterar de uma SKU antiga (herdada) para uma nova SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Tipos de conexão

No modelo de implantação do Gerenciador de recursos, cada configuração requer um tipo específico de conexão de gateway de rede virtual. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

No exemplo do PowerShell a seguir, criamos uma conexão S2S que requer o tipo de conexão *IPSec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipos de VPN

Ao criar o gateway de rede virtual para uma configuração de gateway de VPN, você deve especificar um tipo de VPN. O tipo de VPN que você escolher dependerá da topologia de conexão que você deseja criar. Por exemplo, uma conexão P2S requer um tipo de VPN RouteBased. Um tipo de VPN também pode depender do hardware que você está usando. As configurações S2S exigem um dispositivo VPN. Alguns dispositivos VPN dão suporte apenas a um determinado tipo de VPN.

O tipo de VPN selecionado deve atender a todos os requisitos de conexão para a solução que você deseja criar. Por exemplo, se você quiser criar uma conexão de gateway de VPN S2S e uma conexão de gateway de VPN P2S para a mesma rede virtual, você usaria o tipo de VPN *RouteBased* porque P2S requer um tipo de VPN RouteBased. Você também precisará verificar se o seu dispositivo VPN tem suporte para uma conexão VPN RouteBased. 

Depois que um gateway de rede virtual tiver sido criado, você não poderá alterar o tipo de VPN. Você precisa excluir o gateway de rede virtual e criar um novo. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo do PowerShell a seguir especifica o `-VpnType` como *RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisitos de gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway de VPN, você deve criar uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs e os serviços do gateway de rede virtual usam. Quando você cria seu gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e configuradas com as configurações de gateway de VPN necessárias. Nunca implante mais nada (por exemplo, VMs adicionais) para a sub-rede de gateway. A sub-rede de gateway deve ser nomeada como ' GatewaySubnet ' para funcionar corretamente. Nomear a sub-rede de gateway ' GatewaySubnet ' permite que o Azure saiba que essa é a sub-rede para a qual implantar as VMs e serviços de gateway de rede virtual.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway são alocados para as VMs de gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Ao planejar o tamanho da sub-rede do gateway, consulte a documentação da configuração que você planeja criar. Por exemplo, a configuração de gateway ExpressRoute/VPN coexistente requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, talvez você queira garantir que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede de gateway de/27 ou maior (/27,/26, etc.) se tiver o espaço de endereço disponível para fazer isso. Isso irá acomodar a maioria das configurações.

O exemplo do PowerShell do Gerenciador de recursos a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver a notação CIDR especifica a/27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways de rede local

 Um gateway de rede local é diferente de um gateway de rede virtual. Ao criar uma configuração de gateway de VPN, o gateway de rede local geralmente representa seu local. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local.

Você dá um nome ao gateway de rede local, o endereço IP público do dispositivo VPN local e especifica os prefixos de endereço que estão localizados no local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes de acordo. Você também especifica gateways de rede local para configurações de VNet para VNet que usam uma conexão de gateway de VPN.

O exemplo do PowerShell a seguir cria um novo gateway de rede local:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN for alterado. Consulte [Modificar configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>APIs REST, cmdlets do PowerShell e CLI

Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar APIs REST, cmdlets do PowerShell ou CLI do Azure para configurações de gateway de VPN, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [API REST](https://msdn.microsoft.com/library/jj154113) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Não suportado | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de conexão disponíveis, consulte [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).
