---
title: Anunciar rotas personalizadas para clientes VPN ponto a site | Azure | Microsoft Docs
description: Etapas para anunciar rotas personalizadas para seus clientes de ponto a site
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/26/2019
ms.author: cherylmc
ms.openlocfilehash: 38250d1cd9853013ba9721ece0201a8df6dd1b4a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336299"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anunciar rotas personalizadas para clientes VPN P2S

Talvez você queira anunciar rotas personalizadas para todos os seus clientes VPN ponto a site. Por exemplo, quando você tiver habilitado os pontos de extremidade de armazenamento em sua VNet e quiser que os usuários remotos possam acessar essas contas de armazenamento pela conexão VPN. Você pode anunciar o endereço IP do ponto de extremidade de armazenamento para todos os seus usuários remotos para que o tráfego para a conta de armazenamento vá pelo túnel VPN e não pela Internet pública.

![Exemplo de ligação Multilocal de Gateway de VPN do Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Para anunciar rotas personalizadas

Para anunciar rotas personalizadas, use o `Set-AzVirtualNetworkGateway cmdlet`. O exemplo a seguir mostra como anunciar o IP para as [tabelas de conta de armazenamento da Contoso](https://contoso.table.core.windows.net).

1. Execute ping *contoso.Table.Core.Windows.net* e anote o endereço IP. Por exemplo:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Execute os seguintes comandos do PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Para adicionar várias rotas personalizadas, use um vírgula e espaços para separar os endereços. Por exemplo:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Para exibir rotas personalizadas

Use o exemplo a seguir para exibir rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais de roteamento de P2S, consulte [sobre roteamento ponto a site](vpn-gateway-about-point-to-site-routing.md).
