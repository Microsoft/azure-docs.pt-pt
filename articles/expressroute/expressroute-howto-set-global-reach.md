---
title: 'Configurar o Alcance Global-ExpressRoute: Azure | Microsoft Docs'
description: Este artigo ajuda você a vincular circuitos do ExpressRoute juntos para fazer uma rede privada entre suas redes locais e habilitar o Alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 64abf820a502db0ee0033ce52ed148bae6b8ffc2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748182"
---
# <a name="configure-expressroute-global-reach"></a>Configurar o Alcance Global do ExpressRoute

Este artigo ajuda você a configurar o ExpressRoute Alcance Global usando o PowerShell. Para obter mais informações, consulte [ExpressRouteRoute alcance global](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Você entende os [fluxos de trabalho](expressroute-workflows.md)de provisionamento de circuito do ExpressRoute.
* Os circuitos do ExpressRoute estão em um estado provisionado.
* O emparelhamento privado do Azure é configurado em seus circuitos do ExpressRoute.
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada em seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identificar circuitos

1. Para iniciar a configuração, entre em sua conta do Azure e selecione a assinatura que você deseja usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifique os circuitos do ExpressRoute que você deseja usar. Você pode habilitar o ExpressRoute Alcance Global entre dois circuitos do ExpressRoute, desde que eles estejam localizados nos países/regiões com suporte e criados em diferentes locais de emparelhamento. 

   * Se sua assinatura possuir ambos os circuitos, você poderá escolher um dos circuitos para executar a configuração nas seções a seguir.
   * Se os dois circuitos estiverem em assinaturas diferentes do Azure, você precisará de autorização de uma assinatura do Azure. Em seguida, você passa a chave de autorização ao executar o comando de configuração na outra assinatura do Azure.

## <a name="enable-connectivity"></a>Habilitar conectividade

Habilite a conectividade entre suas redes locais. Há conjuntos separados de instruções para circuitos que estão na mesma assinatura do Azure e circuitos que são assinaturas diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos do ExpressRoute na mesma assinatura do Azure

1. Use os comandos a seguir para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma assinatura.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Execute o comando a seguir no circuito 1 e passe a ID de emparelhamento privado do circuito 2. Ao executar o comando, observe o seguinte:

   * A ID de emparelhamento privado é semelhante ao exemplo a seguir: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* deve ser uma sub-rede IPv4/29, por exemplo, "10.0.0.0/29". Usamos endereços IP nessa sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não deve usar os endereços nessa sub-rede em suas redes virtuais do Azure ou em sua rede local.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Salve a configuração no circuito 1 da seguinte maneira:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior for concluída, você terá conectividade entre suas redes locais em ambos os lados através dos dois circuitos do ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada na assinatura do circuito 2 e a chave de autorização é passada para o circuito 1.

1. Gere uma chave de autorização.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anote a ID de emparelhamento privado do circuito 2, bem como a chave de autorização.
2. Execute o comando a seguir no circuito 1. Passe a ID de emparelhamento privado do circuito 2 e a chave de autorização.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Salve a configuração no circuito 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior for concluída, você terá conectividade entre suas redes locais em ambos os lados através dos dois circuitos do ExpressRoute.

## <a name="verify-the-configuration"></a>Verificar a configuração

Use o comando a seguir para verificar a configuração no circuito em que a configuração foi feita (por exemplo, o circuito 1 no exemplo anterior).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se você simplesmente executar *$CKT 1* no PowerShell, verá *CircuitConnectionStatus* na saída. Ele informa se a conectividade foi estabelecida, "conectada" ou "desconectada". 

## <a name="disable-connectivity"></a>Desabilitar conectividade

Para desabilitar a conectividade entre as redes locais, execute os comandos no circuito em que a configuração foi feita (por exemplo, o circuito 1 no exemplo anterior).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Você pode executar a operação get para verificar o status.

Depois que a operação anterior for concluída, você não terá mais conectividade entre sua rede local por meio de circuitos do ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
1. [Saiba mais sobre o ExpressRoute Alcance Global](expressroute-global-reach.md)
2. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
