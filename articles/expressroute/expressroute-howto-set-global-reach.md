---
title: 'Configure o alcance Global - ExpressRoute: Azure | Microsoft Docs'
description: Este artigo ajuda-o a ligar os circuitos do ExpressRoute em conjunto para garantir uma privada, de rede entre as redes no local e ativar o alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 8ea3b3580cb70d0453a5ec6a38f6063788ebf7f4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082029"
---
# <a name="configure-expressroute-global-reach"></a>Configurar o Alcance Global do ExpressRoute

Este artigo ajuda-o a configurar o ExpressRoute alcance Global com o PowerShell. Para obter mais informações, consulte [alcance Global do ExpressRouteRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Compreender o aprovisionamento do circuito de ExpressRoute [fluxos de trabalho](expressroute-workflows.md).
* Os circuitos do ExpressRoute estão num Estado aprovisionado.
* Peering privado do Azure está configurado em seus circuitos do ExpressRoute.
* Se quiser executar o PowerShell localmente, certifique-se de que a versão mais recente do Azure PowerShell está instalada no seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identificar os circuitos

1. Para iniciar a configuração, inicie sessão na sua conta do Azure e selecione a subscrição que pretende utilizar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifique os circuitos do ExpressRoute que pretende utilizar. Pode ativar o alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estão localizados em países/regiões suportadas e foram criados em diferentes localizações de peering. 

   * Se a sua subscrição é a proprietária de ambos os circuitos, pode escolher qualquer circuito para executar a configuração nas seções a seguir.
   * Se dois circuitos estiverem em diferentes subscrições do Azure, terá de autorização de uma subscrição do Azure. Em seguida, passar a chave de autorização ao executar o comando de configuração na subscrição do Azure.

## <a name="enable-connectivity"></a>Ativar a conectividade

Ative a conectividade entre as redes no local. Existem conjuntos separados de instruções para circuitos que estão na mesma subscrição do Azure e circuitos que estão a subscrições diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos do ExpressRoute na mesma subscrição do Azure

1. Utilize os seguintes comandos para obter o circuito 1 e 2 do circuito. Dois circuitos estão na mesma subscrição.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Execute o seguinte comando na circuito 1 e passar o ID de peering privado do circuito 2. Ao executar o comando, tenha em atenção o seguinte:

   * O ID de peering privado é semelhante ao seguinte exemplo: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* tem de ser/29 IPv4 sub-rede, por exemplo, "10.0.0.0/29". Endereços IP são utilizadas nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não deve usar os endereços nesta sub-rede nas redes virtuais do Azure ou na sua rede no local.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Guarde a configuração no circuito 1 da seguinte forma:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando concluir a operação anterior, que terá conetividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes subscrições do Azure

Se dois circuitos não estiverem na mesma subscrição do Azure, terá de autorização. A seguinte configuração autorização é gerada na subscrição circuito 2, e a chave de autorização é passada para o circuito 1.

1. Gere uma chave de autorização.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anote o ID de peering privado do circuito 2, bem como a chave de autorização.
2. Execute o seguinte comando na circuito 1. Transmita o ID de peering privado do circuito 2 e a chave de autorização.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Guarde a configuração no circuito 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando concluir a operação anterior, que terá conetividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

## <a name="verify-the-configuration"></a>Verificar a configuração

Utilize o seguinte comando para verificar a configuração no circuito onde a configuração foi efetuada (por exemplo, o circuito 1 no exemplo anterior).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se simplesmente executar *us $ckt1* no PowerShell, verá *CircuitConnectionStatus* na saída. Indica se a conectividade é estabelecida, "Ligada" ou "Desconectada". 

## <a name="disable-connectivity"></a>Desativar a conectividade

Para desativar a conectividade entre as redes no local, execute os comandos contra o circuito onde a configuração foi efetuada (por exemplo, circuito 1 no exemplo anterior).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Pode executar a operação Get para verificar o estado.

Depois de concluída a operação anterior, já não tem conectividade entre a rede no local através de circuitos de ExpressRoute.

## <a name="next-steps"></a>Passos Seguintes
1. [Saiba mais sobre o alcance Global do ExpressRoute](expressroute-global-reach.md)
2. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Ligar um circuito do ExpressRoute para uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
