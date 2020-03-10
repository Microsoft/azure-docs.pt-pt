---
title: Rede virtual Legacy Azure VPN gateway SKUs
description: Como trabalhar com a antiga rede virtual gateway SKUs; Básico, Standard e HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379228"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com a rede virtual gateway SKUs (sKUs legado)

Este artigo contém informações sobre o legado (antigo) gateway de rede virtual SKUs. O legado SKUs ainda funciona em ambos os modelos de implementação para gateways VPN que já foram criados. Os portões clássicos da VPN continuam a usar o legado SKUs, tanto para gateways existentes, como para novos gateways. Ao criar novos gateways VPN do Gestor de Recursos, utilize o novo gateway SKUs. Para obter informações sobre as novas SKUs, consulte sobre o [VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Pode ver os preços do gateway legado na secção Gateways de **Rede Virtual,** que está localizado na página de [preços ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="agg"></a>Produção global estimada por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurações suportadas por sKU e tipo VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionar um portal

Você pode redimensionar sua porta de entrada para um portal SKU dentro da mesma família SKU. Por exemplo, se tiver um SKU Standard, pode redimensionar para um SKU de Alto Desempenho. No entanto, não pode redimensionar a sua porta de entrada VPN entre as antigas SKUs e as novas famílias SKU. Por exemplo, não pode ir de um SKU Padrão para um VpnGw2 SKU, ou um SKU básico para VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Para redimensionar uma porta de entrada para o modelo de implementação do Gestor de Recursos utilizando o PowerShell, utilize o seguinte comando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Também pode redimensionar uma porta de entrada no portal Azure.

### <a name="classicresize"></a>Clássico

Para redimensionar uma porta de entrada para o modelo de implementação clássico, deve utilizar os cmdlets powerShell de gestão de serviço. Utilize o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change"></a>Alteração para a nova porta de entrada SKUs

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre as novas SKUs gateway, consulte [Gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku).

Para obter mais informações sobre as definições de configuração, consulte as definições de [configuração do Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).
