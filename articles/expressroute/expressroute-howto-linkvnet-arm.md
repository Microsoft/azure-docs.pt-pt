---
title: 'ExpressRoute: Ligue um VNet a um circuito: Azure PowerShell'
description: Este documento fornece uma visão geral de como ligar redes virtuais (VNets) aos circuitos ExpressRoute utilizando o modelo de implementação do Gestor de Recursos e o PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 108f7ce5639ebd938ed2f4bb5e0c82ac787c4c6c
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737109"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajuda-o a ligar redes virtuais (VNets) aos circuitos Azure ExpressRoute utilizando o modelo de implementação do Gestor de Recursos e o PowerShell. As redes virtuais podem estar na mesma subscrição ou parte de outra subscrição. Este artigo também mostra como atualizar uma ligação de rede virtual.

* Pode ligar até 10 redes virtuais a um circuito Standard ExpressRoute. Todas as redes virtuais devem estar na mesma região geopolítica quando utilizarem um circuito ExpressRoute padrão. 

* Um único VNet pode ser ligado a até quatro circuitos ExpressRoute. Utilize os passos deste artigo para criar um novo objeto de ligação para cada circuito ExpressRoute a que se está a ligar. Os circuitos ExpressRoute podem estar na mesma subscrição, subscrições diferentes ou uma mistura de ambos.

* Pode ligar redes virtuais fora da região geopolítica do circuito ExpressRoute ou ligar um maior número de redes virtuais ao seu circuito ExpressRoute se ativar o add-on premium ExpressRoute. Consulte as [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.


## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos,](expressroute-prerequisites.md) [requisitos de encaminhamento](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e tenha o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o Azure a espreitar para o seu circuito. Consulte o artigo [de encaminhamento de configuração](expressroute-howto-routing-arm.md) para obter instruções de encaminhamento. 
  * Certifique-se de que o espreitamento privado do Azure está configurado e que o BGP está a espreitar entre a sua rede e a Microsoft para que possa ativar a conectividade de ponta a ponta.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criado e totalmente a provisionado. Siga as instruções para [criar uma porta de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute utiliza o GatewayType 'ExpressRoute', não VPN.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligue uma rede virtual na mesma subscrição a um circuito
Pode ligar uma porta de rede virtual a um circuito ExpressRoute utilizando o seguinte cmdlet. Certifique-se de que o gateway de rede virtual é criado e está pronto para ser ligado antes de executar o cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito ExpressRoute através de várias subscrições. A figura a seguir mostra um simples esquema de como a partilha funciona para circuitos ExpressRoute em várias subscrições.

Cada uma das nuvens menores dentro da grande nuvem é usada para representar subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos da organização pode usar a sua própria subscrição para implementar os seus serviços, mas podem partilhar um único circuito ExpressRoute para ligar de volta à sua rede no local. Um único departamento (neste exemplo: IT) pode ser dono do circuito ExpressRoute. Outras subscrições dentro da organização podem usar o circuito ExpressRoute.

> [!NOTE]
> Os custos de conectividade e largura de banda para o circuito ExpressRoute serão aplicados ao proprietário da subscrição. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade de subscrição cruzada](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - proprietários de circuitos e utilizadores de circuitos

O 'proprietário do circuito' é um utilizador autorizado do recurso de energia do circuito ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por "utilizadores de circuitos". Os utilizadores de circuitos são proprietários de gateways de rede virtuais que não estão dentro da mesma subscrição que o circuito ExpressRoute. Os utilizadores de circuitos podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem o poder de modificar e revogar autorizações a qualquer momento. A revogação de uma autorização resulta na suprição de todas as ligações de ligação da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Para criar uma autorização**

O dono do circuito cria uma autorização. Isto resulta na criação de uma chave de autorização que pode ser usada por um utilizador de circuito para ligar os seus gateways de rede virtuais ao circuito ExpressRoute. Uma autorização é válida para apenas uma ligação.

O seguinte corte de cmdlet mostra como criar uma autorização:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


A resposta a esta resposta conterá a chave de autorização e o estado:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Para rever autorizações**

O proprietário do circuito pode rever todas as autorizações emitidas num determinado circuito executando o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para adicionar autorizações**

O proprietário do circuito pode adicionar autorizações utilizando o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para apagar autorizações**

O proprietário do circuito pode revogar/eliminar as autorizações ao utilizador executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operações de utilizador de circuitos

O utilizador do circuito precisa do ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

O ID do par pode ser verificado a partir do seguinte comando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de ligação**

O utilizador do circuito pode executar o seguinte cmdlet para resgatar uma autorização de ligação:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para libertar uma autorização de ligação**

Pode libertar uma autorização eliminando a ligação que liga o circuito ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma ligação de rede virtual
Pode atualizar certas propriedades de uma ligação de rede virtual. 

**Para atualizar o peso da ligação**

A sua rede virtual pode ser ligada a vários circuitos ExpressRoute. Pode receber o mesmo prefixo de mais de um circuito ExpressRoute. Para escolher qual ligação enviar o tráfego destinado a este prefixo, pode alterar o Peso de *Encaminhamento* de uma ligação. O tráfego será enviado na ligação com o mais alto *Peso de Encaminhamento*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

O intervalo de *RoutingWeight* é de 0 a 32000. O valor predefinido é 0.

## <a name="configure-expressroute-fastpath"></a>Configurar ExpressRoute FastPath 
Pode ativar [o ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se o seu gateway de rede virtual for Ultra Performance ou ErGw3AZ. O FastPath melhora o desempenho do caminho dos dados, como pacotes por segundo e ligações por segundo entre a sua rede no local e a sua rede virtual. 

**Configure fastPath em uma nova ligação**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Atualizar uma ligação existente para ativar o FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)
