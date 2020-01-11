---
title: SKUs de gateway de VPN de rede virtual do Azure herdadas
description: Como trabalhar com as SKUs de gateway de rede virtual antigas; Basic, Standard e HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861919"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhando com SKUs de gateway de rede virtual (SKUs herdadas)

Este artigo contém informações sobre as SKUs do gateway de rede virtual herdado (antigo). Os SKUs herdados ainda funcionam em ambos os modelos de implantação para gateways de VPN que já foram criados. Os gateways de VPN clássicos continuam a usar os SKUs herdados, tanto para gateways existentes quanto para novos gateways. Ao criar novos gateways de VPN do Resource Manager, use as novas SKUs de gateway. Para obter informações sobre as novas SKUs, consulte [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Você pode exibir os preços do gateway herdado na seção **gateways de rede virtual** , que está localizada na [página de preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="agg"></a>Taxa de transferência agregada estimada por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurações com suporte por SKU e tipo de VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionar um gateway

Você pode redimensionar o gateway para um SKU de gateway dentro da mesma família de SKU. Por exemplo, se você tiver um SKU padrão, poderá redimensionar para um SKU HighPerformance. No entanto, você não pode redimensionar o gateway de VPN entre os SKUs antigos e as novas famílias de SKU. Por exemplo, você não pode ir de um SKU Standard para um SKU do VpnGw2 ou de uma SKU básica para VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Para redimensionar um gateway para o modelo de implantação do Gerenciador de recursos usando o PowerShell, use o seguinte comando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Você também pode redimensionar um gateway no portal do Azure.

### <a name="classicresize"></a>Clássico

Para redimensionar um gateway para o modelo de implantação clássico, você deve usar os cmdlets do PowerShell de gerenciamento de serviços. Utilize o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change"></a>Alterar para as novas SKUs de gateway

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as novas SKUs de gateway, consulte [SKUs de gateway](vpn-gateway-about-vpngateways.md#gwsku).

Para obter mais informações sobre definições de configuração, consulte [sobre as definições de configuração do gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).
