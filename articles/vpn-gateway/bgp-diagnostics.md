---
title: Ver estado e métricas de BGP
titleSuffix: Azure VPN Gateway
description: Consulte informações importantes relacionadas com o BGP para a resolução de problemas.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 097568dddd5c8568d4523cdeb05ab0c889c31670
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748111"
---
# <a name="view-bgp-metrics-and-status-using-powershell"></a>Ver métricas e estado de BGP usando PowerShell

Utilize **Get-AzVirtualNetworkGatewayBGPPeerStatus** para ver todos os pares de BGP e o estado

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Use **Get-AzVirtualNetworkGatewayLearnedRoute** para ver todas as rotas que o gateway aprendeu através do BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Utilize **o Get-AzVirtualNetworkGatewayAdvertisedRoute** para visualizar todas as rotas que o gateway está a publicitar aos seus pares através do BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos criados, utilize o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para eliminar o grupo de recursos. Este comando elimina o grupo de recursos e todos os recursos que contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).
