---
title: 'Gateway Azure VPN: configurações de configuração'
description: Saiba mais sobre os recursos e configurações do Gateway VPN para uma rede virtual criada no modelo de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010878"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre as definições de configuração do Gateway VPN

Um gateway VPN é um tipo de gateway de rede virtual que envia tráfego encriptado entre a sua rede virtual e a sua localização no local através de uma ligação pública. Também pode usar uma porta de entrada VPN para enviar tráfego entre redes virtuais através da espinha dorsal do Azure.

Uma ligação de gateway VPN baseia-se na configuração de múltiplos recursos, cada um dos quais contém configurações configuráveis. As secções deste artigo discutem os recursos e configurações que se relacionam com uma porta de entrada VPN para uma rede virtual criada no modelo de implementação do Gestor de Recursos. Pode encontrar descrições e diagramas de topologia para cada solução de ligação no artigo sobre a [VPN Gateway.](vpn-gateway-about-vpngateways.md)

Os valores deste artigo aplicam gateways VPN (gateways de rede virtuais que usam o -GatewayType Vpn). Este artigo não abrange todos os tipos de gateways ou portais redundantes de zona.

* Para valores aplicáveis a -GatewayType 'ExpressRoute', consulte [Gateways de Rede Virtual para ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Para portais redundantes de zona, consulte [sobre as portas redundantes da zona.](about-zone-redundant-vnet-gateways.md)

* Para VIRTUAL WAN, consulte [About Virtual WAN](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter uma porta de entrada de rede virtual de cada tipo. Quando estiver a criar um gateway de rede virtual, deve certificar-se de que o tipo de gateway está correto para a sua configuração.

Os valores disponíveis para -GatewayType são:

* Vpn
* ExpressRoute

Um gateway VPN requer a `-GatewayType` *Vpn.*

Exemplo:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Configure um gateway SKU

**Portal do Azure**

Se utilizar o portal Azure para criar um gateway de rede virtual Do Gestor de Recursos, pode selecionar o gateway SKU utilizando o dropdown. As opções que lhe são apresentadas correspondem ao tipo Gateway e ao tipo VPN que seleciona.

**PowerShell**

O exemplo powerShell seguinte especifica como `-GatewaySku` VpnGw1. Ao utilizar o PowerShell para criar um gateway, primeiro tem de criar a configuração IP e, em seguida, utilizar uma variável para se referir a ela. Neste exemplo, a variável de configuração é $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**CLI do Azure**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Redimensionamento ou alteração de um SKU

Se tiver uma porta de entrada VPN e quiser utilizar um SKU de gateway diferente, as suas opções são redimensionar o seu gateway SKU ou mudar para outro SKU. Quando muda para outro gateway SKU, apaga-se completamente o gateway existente e constrói-se um novo. Um portal pode levar até 45 minutos para construir. Em comparação, quando se redimensiona um gateway SKU, não há muito tempo de inatividade porque não é preciso apagar e reconstruir o portal. Se tiver a opção de redimensionar o seu gateway SKU, em vez de alterá-lo, vai querer fazê-lo. No entanto, existem regras relativas ao redimensionamento:

1. Com exceção do SKU Básico, pode redimensionar uma porta de entrada VPN SKU para outro SKU de gateway VPN dentro da mesma geração (Geração1 ou Geração2). Por exemplo, VpnGw1 da Geração1 pode ser redimensionado para VpnGw2 da Geração1, mas não para VpnGw2 da Geração2.
2. Ao trabalhar com os antigos SKU de gateway, pode redimensionar entre SKU Básicos, Standard e de alto desempenho.
3. Não **é possível** redimensionar de SKUs básicos/standard/highperformance para SKUs VPNGw. Em vez disso, tens de [mudar](#change) para os novos SKUs.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Para redimensionar uma porta de entrada

**Portal do Azure**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Para mudar de um antigo (legado) SKU para um novo SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Tipos de ligação

No modelo de implementação do Gestor de Recursos, cada configuração requer um tipo específico de ligação de gateway de rede virtual. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

No exemplo powerShell seguinte, criamos uma ligação S2S que requer o tipo de ligação *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Tipos de VPN

Quando criar o gateway de rede virtual para uma configuração de gateway VPN, deve especificar um tipo VPN. O tipo VPN que escolhe depende da topologia de ligação que pretende criar. Por exemplo, uma ligação P2S requer um tipo VPN RouteBased. Um tipo VPN também pode depender do hardware que está a usar. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN só suportam um determinado tipo VPN.

O tipo VPN que seleciona deve satisfazer todos os requisitos de ligação para a solução que pretende criar. Por exemplo, se pretender criar uma ligação de gateway S2S VPN e uma ligação de gateway P2S VPN para a mesma rede virtual, utilizaria o tipo VPN *RouteBased* porque o P2S requer um tipo VPN RouteBased. Também terá de verificar se o seu dispositivo VPN suportava uma ligação VPN RouteBased. 

Uma vez criado um gateway de rede virtual, não é possível alterar o tipo VPN. Tem de apagar o portal de rede virtual e criar um novo. Existem dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo powerShell seguinte especifica como `-VpnType` *RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Requisitos do gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Sub-rede de gateway

Antes de criar uma porta VPN, tem de criar uma sub-rede de gateway. A sub-rede gateway contém os endereços IP que os VMs e serviços de gateway de rede virtual utilizam. Quando cria o seu gateway de rede virtual, os VMs de gateway são implantados na sub-rede gateway e configurados com as definições de gateway VPN necessárias. Nunca coloque mais nada (por exemplo, VMs adicionais) na sub-rede gateway. A sub-rede gateway deve ser denominada "GatewaySubnet" para funcionar corretamente. Nomear a sub-rede de gateway 'GatewaySubnet' permite ao Azure saber que esta é a sub-rede para implantar os VMs e serviços de gateway de rede virtual.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede gateway são atribuídos aos VMs de gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Quando estiver a planear o tamanho da sub-rede gateway, consulte a documentação para a configuração que está a planear criar. Por exemplo, a configuração coexistição ExpressRoute/VPN Gateway requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, pode querer certificar-se de que a sub-rede gateway contém endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora possa criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede de gateway de /27 ou maior (/27, /26 etc.) se tiver o espaço de endereço disponível para o fazer. Isto acomodará a maioria das configurações.

O exemplo seguinte do Gestor de Recursos PowerShell mostra uma sub-rede de gateway chamada GatewaySubnet. Pode ver que a notação CIDR especifica uma /27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Gateways de rede local

Um portal de rede local é diferente de um portal de rede virtual. Ao criar uma configuração de gateway VPN, o gateway de rede local geralmente representa a sua rede no local e o dispositivo VPN correspondente. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local.

Você dá ao portal de rede local um nome, o endereço IP público ou o nome de domínio totalmente qualificado (FQDN) do dispositivo VPN no local, e especifica os prefixos de endereço que estão localizados no local do local. A Azure olha para os prefixos do endereço de destino para o tráfego de rede, consulta a configuração que especificou para o seu gateway de rede local, e pacotes de rotas em conformidade. Se utilizar o Border Gateway Protocol (BGP) no seu dispositivo VPN, irá fornecer o endereço IP peer BGP do seu dispositivo VPN e o número de sistema autónomo (ASN) da sua rede de instalações. Também especifica os gateways de rede locais para configurações VNet-vNet que utilizam uma ligação de gateway VPN.

O exemplo powerShell a seguir cria um novo portal de rede local:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Às vezes é necessário modificar as definições de gateway de rede local. Por exemplo, quando adiciona ou modifica o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Consulte [modificar as definições de gateway de rede local utilizando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST APIs, PowerShell cmdlets e CLI

Para obter recursos técnicos adicionais e requisitos específicos de sintaxe ao utilizar APIs de REST, cmdlets PowerShell ou Azure CLI para configurações de Gateway VPN, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [API REST](/previous-versions/azure/reference/jj154113(v=azure.100)) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Não suportado | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de ligação disponíveis, consulte [Sobre o Gateway VPN](vpn-gateway-about-vpngateways.md).