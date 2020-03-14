---
title: 'Gateway Azure VPN: configurações de configuração'
description: Saiba mais sobre as definições de VPN Gateway para gateways de rede virtual Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244865"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre as definições de configuração do Gateway VPN

Um gateway VPN é um tipo de gateway de rede virtual que envia tráfego encriptado entre a sua rede virtual e a sua localização no local através de uma ligação pública. Também pode utilizar um portal VPN para enviar tráfego entre redes virtuais através da espinha dorsal do Azure.

Uma ligação de gateway VPN baseia-se na configuração de múltiplos recursos, cada um dos quais contém configurações configuráveis. As secções deste artigo discutem os recursos e configurações que se relacionam com uma porta de entrada VPN para uma rede virtual criada no modelo de implementação do Gestor de Recursos. Pode encontrar descrições e diagramas de topologia para cada solução de ligação no artigo [sobre vpn Gateway.](vpn-gateway-about-vpngateways.md)

Os valores deste artigo aplicam gateways VPN (gateways de rede virtual que utilizam o Vpn -GatewayType). Este artigo não abrange todos os tipos de gateway ou gateways redundantes da zona.

* Para valores aplicáveis a -GatewayType 'ExpressRoute', consulte [Gateways de Rede Virtual para ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Para gateways redundantes de zona, consulte [sobre gateways redundantes](about-zone-redundant-vnet-gateways.md)em zona .

* Para O WAN Virtual, consulte [About Virtual WAN](../virtual-wan/virtual-wan-about.md).



## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter um portal de rede virtual de cada tipo. Quando estiver a criar um portal de rede virtual, deve certificar-se de que o tipo de gateway está correto para a sua configuração.

Os valores disponíveis para -GatewayType são:

* Vpn
* ExpressRoute

Um gateway VPN requer o `-GatewayType` *Vpn*.

Exemplo:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Configure um portal SKU

#### <a name="azure-portal"></a>Portal do Azure

Se utilizar o portal Azure para criar um portal de rede virtual do Gestor de Recursos, pode selecionar o gateway SKU utilizando o dropdown. As opções com que lhe são apresentadas correspondem ao tipo Gateway e ao tipo VPN que seleciona.

#### <a name="powershell"></a>PowerShell

O exemplo powerShell seguinte especifica o `-GatewaySku` como VpnGw1. Ao utilizar o PowerShell para criar um portal, primeiro tem de criar a configuração IP e, em seguida, usar uma variável para se referir a ela. Neste exemplo, a variável de configuração é $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Redimensionamento ou alteração de um SKU

Se tiver um gateway VPN e pretender utilizar um SKU de gateway diferente, as suas opções são para redimensionar o seu gateway SKU, ou mudar para outro SKU. Quando muda para outra porta de entrada SKU, elimina totalmente a porta de entrada existente e constrói uma nova. Um portal pode levar até 45 minutos para construir. Em comparação, quando redimensiona um Portal SKU, não há muito tempo de inatividade porque não tem de apagar e reconstruir o portal. Se tiver a opção de redimensionar o seu portal SKU, em vez de o alterar, vai querer fazê-lo. No entanto, existem regras relativas à redimensionamento:

1. Com exceção do SKU Básico, pode redimensionar um Gateway SKU VPN para outro Gateway VpN SKU dentro da mesma geração (Geração1 ou Geração2). Por exemplo, vpnGw1 da Geração 1 pode ser redimensionado para VpnGw2 da Geração 1, mas não para VpnGw2 da Geração 2.
2. Ao trabalhar com os antigos SKU de gateway, pode redimensionar entre SKU Básicos, Standard e de alto desempenho.
3. Não **é possível** redimensionar de Basic/Standard/HighPerformance SKUs para VpnGw SKUs. Em vez disso, tens de [mudar](#change) para as novas SKUs.

#### <a name="resizegwsku"></a>Para redimensionar um portal

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Para mudar de um velho (legado) SKU para um novo SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Tipos de ligação

No modelo de implementação do Gestor de Recursos, cada configuração requer um tipo específico de ligação de gateway de rede virtual. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

No exemplo seguinte powerShell, criamos uma ligação S2S que requer o tipo de ligação *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipos VPN

Quando criar o portal de rede virtual para uma configuração de gateway VPN, deve especificar um tipo VPN. O tipo VPN que escolher depende da topologia de ligação que pretende criar. Por exemplo, uma ligação P2S requer um tipo VPN baseado em rota. Um tipo VPN também pode depender do hardware que está a usar. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN suportam apenas um certo tipo de VPN.

O tipo VPN selecionado deve satisfazer todos os requisitos de ligação para a solução que pretende criar. Por exemplo, se pretender criar uma ligação de gateway S2S VPN e uma ligação p2S VPN para a mesma rede virtual, utilizaria o tipo VPN *RouteBased* porque o P2S requer um tipo VPN baseado em rotas. Também precisa de verificar se o seu dispositivo VPN suportava uma ligação VPN baseada em RouteBased. 

Uma vez criado um portal de rede virtual, não é possível alterar o tipo VPN. Tem de apagar o portal da rede virtual e criar um novo. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo powerShell seguinte especifica o `-VpnType` como *RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisitos de gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sub-rede gateway

Antes de criar um gateway VPN, tem de criar uma sub-rede de gateway. A sub-rede gateway contém os endereços IP que os VMs e serviços de gateway de rede virtual utilizam. Quando cria o gateway de rede virtual, os VMs de gateway são implantados na subnet gateway e configurados com as definições de gateway VPN necessárias. Nunca coloque mais nada (por exemplo, VMs adicionais) na sub-rede gateway. A sub-rede gateway deve ser denominada 'GatewaySubnet' para funcionar corretamente. Nomear a subnet 'GatewaySubnet' permite ao Azure saber que esta é a subrede para implantar os VMs e serviços de gateway de rede virtual para.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na subnet gateway são atribuídos aos VMs gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Quando estiver a planear o tamanho da sua subnet gateway, consulte a documentação para a configuração que está a planear criar. Por exemplo, a configuração coexistl ExpressRoute/VPN Gateway requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, pode querer certificar-se de que a sua subnet de gateway contém endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora possa criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede de entrada de /27 ou maior (/27, /26 etc.) se tiver o espaço de endereço disponível para o fazer. Isto acomodará a maioria das configurações.

O exemplo powerShell do Gestor de Recursos que se segue mostra uma subnet de gateway chamada GatewaySubnet. Pode ver que a notação CIDR especifica um /27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways de rede local

 Um portal de rede local é diferente de um portal de rede virtual. Ao criar uma configuração de gateway VPN, o gateway de rede local geralmente representa a sua localização no local. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local.

Dá um nome ao portal de rede local, o endereço IP público do dispositivo VPN no local e especifica os prefixos de endereço que estão localizados no local no local. O Azure analisa os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que especificou para o gateway da rede local e rotas em conformidade. Também especifica os gateways de rede locais para configurações VNet-to-VNet que utilizam uma ligação de gateway VPN.

O exemplo da PowerShell cria um novo portal de rede local:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Por vezes, é necessário modificar as definições de gateway da rede local. Por exemplo, quando adiciona ou modifica a gama de endereços, ou se o endereço IP do dispositivo VPN mudar. Consulte modificar as definições de gateway de [rede locais utilizando powerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>APIs REST, PowerShell cmdlets e CLI

Para recursos técnicos adicionais e requisitos específicos de sintaxe ao utilizar APIs REST, PowerShell cmdlets ou Azure CLI para configurações VPN Gateway, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [API REST](https://msdn.microsoft.com/library/jj154113) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Não suportado | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de ligação disponíveis, consulte [o VPN Gateway](vpn-gateway-about-vpngateways.md).
