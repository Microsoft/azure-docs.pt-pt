---
title: 'Tutorial - Azure ExpressRoute: Adicione uma porta de entrada a um VNet - Azure PowerShell'
description: Este tutorial ajuda-o a adicionar gateway VNet a um VNet gestor de recursos já criado para ExpressRoute usando a Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2b75e6e0a8b79f374900e6cb2dfc49680d3d0190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739063"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Tutorial: Configurar uma porta de rede virtual para ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Gestor de Recursos - Portal Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Este tutorial ajuda-o a adicionar, a redimensionar e a remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração aplicam-se aos VNets que foram criados utilizando o modelo de implementação do Gestor de Recursos para uma configuração ExpressRoute. Para mais informações, consulte [sobre as portas de rede virtuais para ExpressRoute](expressroute-about-virtual-network-gateways.md).

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Crie uma sub-rede de gateway.
> - Criar gateway de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="configuration-reference-list"></a>Lista de referência de configuração

Os passos para esta tarefa utilizam um VNet com base nos valores da seguinte lista de referência de configuração. Definições e nomes adicionais também estão descritos nesta lista. Não usamos esta lista diretamente em nenhum dos passos, embora adicionemos variáveis com base nos valores desta lista. Pode copiar a lista para usar como referência, substituindo os valores pelos seus.

| Definição                   | Valor                                              |
| ---                       | ---                                                |
| Nome da rede virtual | *TestVNet* |    
| Espaço de endereço de rede virtual | *192.168.0.0/16* |
| Grupo de Recursos | *TestRG* |
| Nome subneta1 | *FrontEnd* |   
| Espaço de endereço subnet1 | *192.168.1.0/24* |
| Nome subneta1 | *FrontEnd* |
| Nome da sub-rede Gateway | *GatewaySubnet* |    
| Espaço de endereço da Sub-rede Gateway | *192.168.200.0/26* |
| Region | *E.U.A. Leste* |
| Nome do Gateway | *GW* |   
| Nome IP gateway | *GWIP* |
| Nome de configuração IP gateway | *gwipconf* |
| Tipo | *ExpressRoute* |
| Gateway Nome IP Público  | *gwpip* |

> [!IMPORTANT]
> O apoio iPv6 ao peering privado está atualmente em **Visualização Pública**. Se pretender ligar a sua rede virtual a um circuito ExpressRoute com o espremo privado com base no IPv6, certifique-se de que a sua rede virtual é dupla pilha e segue as diretrizes [descritas aqui.](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)
> 
> 

## <a name="add-a-gateway"></a>Adicionar um gateway

1. Para ligar com Azure, corra `Connect-AzAccount` .

1. Declare as suas variáveis para este exercício. Certifique-se de editar a amostra para refletir as definições que pretende utilizar.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Guarde o objeto de rede virtual como uma variável.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Adicione uma sub-rede de gateway à sua Rede Virtual. A sub-rede gateway deve ser denominada "GatewaySubnet". A sub-rede gateway tem de ser /27 ou maior (/26, /25, e assim por diante).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Se estiver a utilizar uma rede virtual de pilha dupla e planeie utilizar o espreitamento privado baseado no IPv6 sobre o ExpressRoute, crie uma sub-rede de gateway de pilha dupla.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Defina a configuração.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Guarde a sub-rede gateway como variável.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Solicitar um endereço IP público. O endereço IP é solicitado antes de criar o gateway. Não é possível especificar o endereço IP que pretende utilizar; é atribuído dinamicamente. Utilizará este endereço IP na secção de configuração seguinte. O "A Atribuição de Café" deve ser dinâmico.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Crie a configuração para o seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Neste passo, está a especificar a configuração que será usada quando criar o gateway. Utilize o exemplo seguinte para criar a configuração do gateway.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Criar o portal. Neste passo, o **-GatewayType** é especialmente importante. Deve utilizar o valor **ExpressRoute**. Depois de executar estes cmdlets, o gateway pode demorar 45 minutos ou mais a criar.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Se planeia utilizar o peering privado baseado no IPv6 sobre o ExpressRoute, certifique-se de selecionar um AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) para **-GatewaySku**.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Verifique se o portal foi criado
Utilize os seguintes comandos para verificar se o gateway foi criado:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Resize a gateway (Redimensionar um gateway)
Há uma série de [SKUs gateway.](expressroute-about-virtual-network-gateways.md) Pode utilizar o seguinte comando para alterar o Gateway SKU a qualquer momento.

> [!IMPORTANT]
> Este comando não funciona para o Portal UltraPerformance. Para alterar a sua porta de entrada para um gateway UltraPerformance, primeiro remova o gateway ExpressRoute existente e, em seguida, crie um novo gateway UltraPerformance. Para desclassificar o seu portal a partir de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, crie um novo portal.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Limpar os recursos
Utilize o seguinte comando para remover o gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Passos seguintes
Depois de criar o gateway VNet, pode ligar o seu VNet a um circuito ExpressRoute. 

> [!div class="nextstepaction"]
> [Ligue uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
