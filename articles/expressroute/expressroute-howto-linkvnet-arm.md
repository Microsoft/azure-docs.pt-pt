---
title: 'Vincular uma rede virtual a um circuito de ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este documento fornece uma visão geral de como vincular redes virtuais (VNets) a circuitos de ExpressRoute usando o modelo de implantação do Gerenciador de recursos e o PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: d4b746a245fc1ee2b0d3532bfabc973f513c7661
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748290"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Conectar uma rede virtual a um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [portal do Azure de vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajuda você a vincular redes virtuais (VNets) a circuitos do Azure ExpressRoute usando o modelo de implantação do Gerenciador de recursos e o PowerShell. As redes virtuais podem estar na mesma assinatura ou em parte de outra assinatura. Este artigo também mostra como atualizar um link de rede virtual.

* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais devem estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão. 

* Uma única VNet pode ser vinculada a até quatro circuitos do ExpressRoute. Use as etapas neste artigo para criar um novo objeto de conexão para cada circuito de ExpressRoute ao qual você está se conectando. Os circuitos do ExpressRoute podem estar na mesma assinatura, em assinaturas diferentes ou em uma mistura de ambos.

* Você pode vincular redes virtuais fora da região geopolítica do circuito do ExpressRoute ou conectar um número maior de redes virtuais ao circuito do ExpressRoute se você tiver habilitado o complemento Premium do ExpressRoute. Verifique as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento Premium.


## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md), [os requisitos de roteamento](expressroute-routing.md)e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e fazer com que o circuito seja habilitado pelo seu provedor de conectividade. 
  * Verifique se você tem o emparelhamento privado do Azure configurado para seu circuito. Consulte o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções de roteamento. 
  * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento via protocolo BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se você tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o Gatewaytype ' ExpressRoute ', e não VPN.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito
Você pode conectar um gateway de rede virtual a um circuito de ExpressRoute usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e está pronto para vinculação antes de executar o cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como o compartilhamento funciona para circuitos do ExpressRoute em várias assinaturas.

Cada uma das nuvens menores na nuvem grande é usada para representar assinaturas que pertencem a diferentes departamentos em uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas eles podem compartilhar um único circuito do ExpressRoute para se conectar de volta à sua rede local. Um único departamento (neste exemplo: ele) pode possuir o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito do ExpressRoute.

> [!NOTE]
> Os encargos de conectividade e largura de banda para o circuito do ExpressRoute serão aplicados ao proprietário da assinatura. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administração-proprietários de circuito e usuários de circuito

O ' proprietário do circuito ' é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por ' usuários do circuito '. Os usuários de circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito de ExpressRoute. Os usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização resulta na exclusão de todas as conexões de link da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário de circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida para apenas uma conexão.

O trecho de cmdlet a seguir mostra como criar uma autorização:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


A resposta a isso conterá a chave de autorização e o status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Para revisar autorizações**

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para adicionar autorizações**

O proprietário do circuito pode adicionar autorizações usando o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Para excluir autorizações**

O proprietário do circuito pode revogar/excluir autorizações para o usuário executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operações de usuário do circuito

O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

A ID de par pode ser verificada a partir do seguinte comando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de conexão**

O usuário do circuito pode executar o seguinte cmdlet para resgatar uma autorização de link:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para liberar uma autorização de conexão**

Você pode liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma conexão de rede virtual
Você pode atualizar determinadas propriedades de uma conexão de rede virtual. 

**Para atualizar o peso da conexão**

Sua rede virtual pode ser conectada a vários circuitos do ExpressRoute. Você pode receber o mesmo prefixo de mais de um circuito do ExpressRoute. Para escolher a conexão para enviar o tráfego destinado a esse prefixo, você pode alterar *RoutingWeight* de uma conexão. O tráfego será enviado na conexão com o *RoutingWeight*mais alto.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

O intervalo de *RoutingWeight* é de 0 a 32000. O valor padrão é 0.

## <a name="configure-expressroute-fastpath"></a>Configurar o ExpressRoute FastPath 
Você pode habilitar o [Expressroute FastPath](expressroute-about-virtual-network-gateways.md) se o circuito do expressroute estiver no [expressroute Direct](expressroute-erdirect-about.md) e seu gateway de rede virtual for ultra performance ou ErGw3AZ. O FastPath melhora o desempenho do caminho de dados, como pacotes por segundo e conexões por segundo entre sua rede local e sua rede virtual. 

> [!NOTE] 
> Se você já tiver uma conexão de rede virtual, mas não tiver habilitado FastPath, precisará excluir a conexão de rede virtual e criar uma nova. 
> 
>  

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
