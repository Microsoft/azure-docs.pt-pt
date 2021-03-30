---
title: 'Azure VPN Gateway: Gestão de sessão VPN ponto-a-local'
description: Este artigo ajuda-o a visualizar e desligar as sessões VPN ponto-a-local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91274238"
---
# <a name="point-to-site-vpn-session-management"></a>Gestão de sessão VPN ponto-a-local

Os gateways de rede virtuais Azure proporcionam uma maneira fácil de visualizar e desligar as atuais sessões VPN ponto-a-local. Este artigo ajuda-o a visualizar e desligar as sessões atuais.

>[!NOTE]
>O estado da sessão é atualizado a cada 5 minutos. Não é atualizado imediatamente.
>

## <a name="portal"></a>Portal

Para visualizar e desligar uma sessão no portal:

1. Navegue até ao Gateway de VPN.
1. Na secção **de Monitorização,** selecione **Sessões ponto a local**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Exemplo do portal":::
1. Pode ver todas as sessões atuais na vidraça.
1. Selecione **"..."** para a sessão que pretende desligar e, em seguida, **selecione Desligar**.

## <a name="powershell"></a>PowerShell

Para visualizar e desligar uma sessão utilizando o PowerShell:

1. Executar o seguinte comando PowerShell para listar sessões ativas:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Copie o **VpnConnectionId** da sessão que pretende desligar.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Exemplo do PowerShell":::
1. Para desligar a sessão, executar o seguinte comando:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as ligações ponto-a-local, consulte [a VPN ponto-a-local](point-to-site-about.md).
