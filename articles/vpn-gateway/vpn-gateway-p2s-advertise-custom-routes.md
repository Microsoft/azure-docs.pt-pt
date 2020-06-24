---
title: 'Azure VPN Gateway: Anuncie rotas personalizadas para clientes P2S VPN'
description: Passos para anunciar rotas personalizadas para os seus clientes ponto-a-local
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: ad007514e48ea751257884ba6e9ccb3965442d36
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987577"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anuncie rotas personalizadas para clientes P2S VPN

Você pode querer anunciar rotas personalizadas para todos os seus clientes VPN ponto a local. Por exemplo, quando tiver ativado os pontos finais de armazenamento no seu VNet e pretender que os utilizadores remotos possam aceder a estas contas de armazenamento sobre a ligação VPN. Pode anunciar o endereço IP do ponto final de armazenamento a todos os seus utilizadores remotos para que o tráfego para a conta de armazenamento se sobreponhe pelo túnel VPN e não pela Internet pública.

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Para anunciar rotas personalizadas

Para anunciar rotas personalizadas, use o `Set-AzVirtualNetworkGateway cmdlet` . O exemplo a seguir mostra como anunciar o IP para as tabelas de [conta de armazenamento Contoso.](https://contoso.table.core.windows.net)

1. Ping *contoso.table.core.windows.net* e anotar o endereço IP. Por exemplo:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Execute os seguintes comandos do PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Para adicionar várias rotas personalizadas, utilize uma vírgula e espaços para separar os endereços. Por exemplo:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Para ver rotas personalizadas

Use o seguinte exemplo para visualizar rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Para eliminar rotas personalizadas

Utilize o seguinte exemplo para eliminar rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais sobre o encaminhamento P2S, consulte [sobre o encaminhamento ponto-a-local](vpn-gateway-about-point-to-site-routing.md).
