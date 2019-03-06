---
title: 'Ligar uma rede virtual a um circuito do ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este documento fornece uma descrição geral de como ligar redes virtuais (VNets) para circuitos do ExpressRoute com o modelo de implementação do Resource Manager e PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/30/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: b0ae3d5b2c60badfb9b59820b1e0b85383e524ca
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406325"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ligar uma rede virtual a um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajuda-o a ligar redes virtuais (VNets) para circuitos do ExpressRoute do Azure com o modelo de implementação do Resource Manager e PowerShell. Redes virtuais podem estar na mesma subscrição ou parte de outra subscrição. Este artigo também mostra como atualizar uma ligação de rede virtual.

* Pode ligar até 10 redes virtuais a um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito de ExpressRoute standard. 

* Pode ser associada uma VNet única para até quatro circuitos do ExpressRoute. Utilize os passos neste artigo para criar um novo objeto de ligação para cada circuito de ExpressRoute que está a ligar. Os circuitos do ExpressRoute podem ser na mesma subscrição, subscrições diferentes ou uma combinação de ambos.

* Pode ligar redes virtuais fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o seu circuito do ExpressRoute, se tiver ativado o suplemento ExpressRoute premium. Verifique os [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.


## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e ter o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte a [configurar o encaminhamento](expressroute-howto-routing-arm.md) artigo para obter instruções de encaminhamento. 
  * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está ativo, para que pode habilitar a conectividade de ponto-a-ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para o ExpressRoute utiliza o GatewayType "ExpressRoute", não VPN.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito
Pode ligar um gateway de rede virtual a um circuito do ExpressRoute com o cmdlet seguinte. Certifique-se de que o gateway de rede virtual é criado e está pronto para a ligação antes de executar o cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito do ExpressRoute em várias subscrições. A figura seguinte mostra um simples esquemática funciona como a partilha dos circuitos do ExpressRoute em várias subscrições.

Cada uma das nuvens menores na cloud da grande é utilizada para representar as subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode utilizar a sua própria subscrição para a implementação de seus serviços, mas eles podem partilhar um único circuito de ExpressRoute para ligar a voltar à sua rede no local. Um único departamento (neste exemplo: IT) pode ser proprietário do circuito do ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito do ExpressRoute.

> [!NOTE]
> Custos de conectividade e largura de banda do circuito do ExpressRoute serão aplicados ao proprietário da subscrição. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - proprietários de circuito e os utilizadores do circuito

O "proprietário do circuito' é um utilizador autorizado de energia do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por "utilizadores do circuito". Os utilizadores do circuito são proprietários de gateways de rede virtual que não estão na mesma subscrição que o circuito do ExpressRoute. Os utilizadores do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem o poder para modificar e revogar autorizações em qualquer altura. Revogar um resultados de autorização em todas as conexões de ligação a ser eliminados da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os seus gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

O fragmento de cmdlet seguinte mostra como criar uma autorização:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


A resposta a esta irá conter a chave de autorização e o estado:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Para rever as autorizações**

O proprietário do circuito pode rever todas as autorizações emitidos num determinado circuito, execute o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para adicionar as autorizações**

O proprietário do circuito pode adicionar as autorizações utilizando o cmdlet seguinte:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para eliminar autorizações**

O proprietário do circuito pode revogar/eliminar autorizações para o usuário executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador de circuito tem o ID de elemento de rede e uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

ID de ponto a ponto pode ser verificado no comando seguinte:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de conexão**

O utilizador de circuito pode executar o cmdlet seguinte para resgatar uma autorização de ligação:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para liberar uma autorização de conexão**

Pode libertar uma autorização ao eliminar a ligação que ligue o circuito do ExpressRoute para a rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma ligação de rede virtual
Pode atualizar determinadas propriedades de uma ligação de rede virtual. 

**Para atualizar o peso de ligação**

A rede virtual pode ser ligada a vários circuitos do ExpressRoute. Poderá receber o mesmo prefixo de mais do que um circuito do ExpressRoute. Para escolher qual a ligação para enviar o tráfego destinado a este prefixo, pode alterar *RoutingWeight* de uma ligação. Será possível enviar o tráfego na conexão com a mais alta *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

O intervalo de *RoutingWeight* é 0 para 32000. O valor predefinido é 0.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
