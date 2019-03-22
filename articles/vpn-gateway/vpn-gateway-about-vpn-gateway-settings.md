---
title: Definições do gateway de VPN para ligações do Azure entre locais | Documentos da Microsoft
description: Saiba mais sobre as definições do Gateway de VPN para gateways de rede virtual do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 76323ab00a3562cae10520b18008d030e40043fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864682"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre as definições de configuração do Gateway de VPN

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego encriptado entre a rede virtual e sua localização no local através de uma ligação pública. Também pode utilizar um gateway de VPN para enviar tráfego entre redes virtuais entre o backbone do Azure.

Uma ligação de gateway VPN depende da configuração de vários recursos, cada um deles contém definições configuráveis. As secções neste artigo abordam os recursos e as definições relacionadas com a um gateway VPN para uma rede virtual criada no modelo de implementação do Resource Manager. Encontrará descrições e diagramas de topologia para cada solução de ligação no [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) artigo.

Os valores neste artigo aplicam-se a gateways de VPN (gateways de rede virtual que utilizam - GatewayType Vpn). Este artigo não abrange todos os tipos de gateway ou gateways com redundância de zona.

* Para valores que se aplicam ao - GatewayType "ExpressRoute", consulte [Gateways de rede Virtual para o ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Para gateways com redundância de zona, veja [sobre os gateways com redundância de zona](about-zone-redundant-vnet-gateways.md).

* Para o Virtual WAN, consulte [sobre a WAN Virtual](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter um gateway de rede virtual de cada tipo. Quando estiver a criar um gateway de rede virtual, deve certificar-se de que o tipo de gateway está correto para a sua configuração.

Os valores disponíveis para o-GatewayType são:

* Vpn
* ExpressRoute

Requer um gateway de VPN a `-GatewayType` *Vpn*.

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

Se utilizar o portal do Azure para criar um gateway de rede virtual do Resource Manager, pode selecionar o SKU de gateway com a lista pendente. As opções que são apresentados com correspondem para o tipo de Gateway e o tipo VPN que selecionar.

#### <a name="powershell"></a>PowerShell

O exemplo de PowerShell seguinte especifica o `-GatewaySku` como VpnGw1. Quando utilizar o PowerShell para criar um gateway, é preciso primeiro criar a configuração de IP, em seguida utilizar uma variável para fazer referência a ele. Neste exemplo, a variável de configuração é $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Redimensionar ou alterar um SKU

Se tiver um gateway VPN e pretender utilizar um SKU de gateway diferente, as opções são a redimensionar o SKU de gateway ou alterar para outro SKU. Quando mudar para outro SKU de gateway, inteiramente a eliminar o gateway existente e criar um novo. Um gateway pode demorar até 45 minutos a criar. Em comparação, quando redimensiona um gateway do SKU, não há período de indisponibilidade porque não tem de eliminar e recriar o gateway. Se tiver a opção para redimensionar o SKU de gateway, em vez de alterá-la, desejará fazer isso. No entanto, existem regras sobre o redimensionamento:

1. Pode redimensionar entre SKU VpnGw1, VpnGw2 e VpnGw3.
2. Ao trabalhar com os antigos SKU de gateway, pode redimensionar entre SKU Básicos, Standard e de alto desempenho.
3. **Não é possível** redimensionar SKU Básicos/Standard/alto desempenho para SKU VpnGw2/VpnGw1/VpnGw3 novos. Em vez disso, tem [alterar](#change) para os novos SKU.

#### <a name="resizegwsku"></a>Para redimensionar um gateway

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Para alterar um SKU (herdado) antigo para um novo SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Tipos de ligação

No modelo de implementação do Resource Manager, cada configuração exige um tipo de ligação de gateway de rede virtual específica. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

O exemplo do PowerShell seguinte, vamos criar uma ligação de S2S, que requer que o tipo de ligação *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipos de VPN

Ao criar o gateway de rede virtual para uma configuração de gateway VPN, tem de especificar um tipo de VPN. O tipo de VPN que escolher depende da topologia de ligação que pretende criar. Por exemplo, uma ligação P2S exige um tipo de RouteBased VPN. Um tipo de VPN também pode confiar no hardware que está a utilizar. Configurações de S2S requerem um dispositivo VPN. Alguns dispositivos VPN só suportam um determinado tipo VPN.

O tipo VPN que selecionar tem de cumprir todos os requisitos de ligação para a solução que pretende criar. Por exemplo, se quiser criar uma ligação de gateway de S2S VPN e uma ligação de gateway de P2S VPN para a mesma rede virtual, usaria o tipo VPN *RouteBased* porque P2S exige um tipo de RouteBased VPN. Também precisaria verificar se o dispositivo VPN suportada uma ligação de RouteBased VPN. 

Assim que tiver sido criado um gateway de rede virtual, não é possível alterar o tipo de VPN. Terá de eliminar o gateway de rede virtual e criar um novo. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo de PowerShell seguinte especifica os `-VpnType` como *RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisitos do gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway de VPN, tem de criar uma sub-rede de gateway. A sub-rede do gateway contém os endereços IP que utilizam os serviços e VMs de gateway de rede virtual. Ao criar o gateway de rede virtual, VMs de gateway são implementadas na sub-rede de gateway e configuradas com as definições do gateway VPN necessárias. Nunca implemente qualquer outra coisa (por exemplo, VMs adicionais) para a sub-rede do gateway. A sub-rede do gateway tem de ser o nome "GatewaySubnet" funcione corretamente. A sub-rede do gateway de nomenclatura "GatewaySubnet" permite que o Azure saiba que se trata de sub-rede para implementar o gateway de rede virtual VMs e serviços para.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway são alocados para as VMs de gateway e os serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. Veja as instruções para a configuração que pretende criar e certifique-se de que a sub-rede do gateway que pretende criar cumpre os requisitos. Além disso, deverá certificar-se de que a sub-rede do gateway contém endereços IP suficientes suportem possíveis configurações adicionais futuras. Embora possa criar uma sub-rede de gateway tão pequena como/29, recomendamos que crie uma sub-rede de gateway de/28 ou superior (/ 28, / 27, /26 etc.). Dessa forma, se adicionar a funcionalidade no futuro, não precisará há o gateway, em seguida, eliminar e recriar a sub-rede do gateway para permitir mais endereços IP.

O exemplo de PowerShell do Resource Manager seguinte mostra uma sub-rede de gateway com o nome GatewaySubnet. Pode ver que a notação CIDR Especifica/27, que permite endereços IP suficientes na maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways de rede local

 Um gateway de rede local é diferente de um gateway de rede virtual. Ao criar uma configuração de gateway VPN, o gateway de rede local representa, normalmente, sua localização no local. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local.

Dê um nome, o endereço IP público do dispositivo VPN no local, ao gateway de rede local e especificar os prefixos de endereços que se encontram na localização no local. O Azure analisa os prefixos de endereço de destino para tráfego de rede, consulta a configuração especificada para o seu gateway de rede local e encaminha os pacotes em conformidade. Também especificar gateways de rede local para as configurações de VNet a VNet que utilizem uma ligação de gateway VPN.

O exemplo de PowerShell seguinte cria um novo gateway de rede local:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Por vezes, terá de modificar as definições do gateway de rede local. Por exemplo, se adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN é alterado. Ver [modificar as definições do gateway de rede local com o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>APIs, cmdlets do PowerShell, REST e a CLI

Para recursos técnicos adicionais e requisitos de sintaxe específica ao utilizar REST APIs, cmdlets do PowerShell ou da CLI do Azure para configurações de Gateway de VPN, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azurerm.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [API REST](https://msdn.microsoft.com/library/jj154113) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Não suportado | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre configurações de ligação disponíveis, consulte [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).
