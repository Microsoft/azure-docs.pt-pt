---
title: Ver estado e métricas de BGP
titleSuffix: Azure VPN Gateway
description: Consulte informações importantes relacionadas com o BGP para a resolução de problemas.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103148851"
---
# <a name="view-bgp-metrics-and-status"></a>Ver métricas e estado do BGP

Pode ver métricas e estado de BGP utilizando o portal Azure ou utilizando a Azure PowerShell.

## <a name="azure-portal"></a>Portal do Azure

No portal Azure, pode ver os pares BGP, rotas aprendidas e rotas anunciadas. Também pode descarregar .csv ficheiros que contenham estes dados.

1. No [portal Azure,](https://portal.azure.com)navegue até ao seu portal de rede virtual.
1. Em **Monitorização,** selecione **pares BGP** para abrir a página de pares BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Screenshot de métricas no portal Azure.":::

### <a name="learned-routes"></a>Rotas aprendidas

1. Pode ver até 50 rotas aprendidas no portal.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Screenshot de rotas aprendidas.":::

1. Também pode descarregar o ficheiro de rotas aprendidas. Se tiver mais de 50 rotas aprendidas, a única maneira de vê-las todas é descarregando e visualizando o ficheiro .csv. Para descarregar, **selecione Baixar as rotas aprendidas.**

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Screenshot de descarregar rotas aprendidas.":::
1. Então, veja o ficheiro.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Screenshot de rotas aprendidas descarregadas.":::

### <a name="advertised-routes"></a>Rotas anunciadas

1. Para visualizar as rotas anunciadas, selecione o **...** no final da rede que pretende visualizar e, em seguida, clique em **Ver Rotas anunciadas**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Screenshot mostrando como ver rotas anunciadas." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Nas **Rotas anunciadas para página de pares,** pode ver até 50 rotas anunciadas.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Screenshot de rotas anunciadas.":::
1. Também pode descarregar o ficheiro de rotas anunciado. Se tem mais de 50 rotas anunciadas, a única forma de as visualizar todas é descarregando e visualizando o ficheiro .csv. Para baixar, **selecione Baixar as rotas anunciadas.**

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Screenshot de selecionar rotas publicitadas descarregadas.":::
1. Então, veja o ficheiro.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Screenshot de rotas publicitadas descarregadas.":::

### <a name="bgp-peers"></a>Pares BGP

1. Pode ver até 50 pares BGP no portal.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Screenshot dos pares da BGP.":::
1. Também pode descarregar o ficheiro de pares BGP. Se tiver mais de 50 pares BGP, a única maneira de vê-los todos é descarregando e visualizando o ficheiro .csv. Para baixar, selecione **Baixar os pares BGP** na página do portal.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Screenshot de descarregar pares BGP.":::
1. Então, veja o ficheiro.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Screenshot de colegas BGP descarregados.":::

## <a name="powershell"></a>PowerShell

Utilize **o Get-AzVirtualNetworkGatewayBGPPeerStatus** para ver todos os pares de BGP e o estado.

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o BGP, consulte [o Configure BGP para VPN Gateway](bgp-howto.md).
