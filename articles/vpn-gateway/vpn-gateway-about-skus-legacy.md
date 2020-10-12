---
title: Legacy Azure rede virtual VPN gateway SKUs
description: Como trabalhar com o antigo gateway de rede virtual SKUs; Básico, Standard e HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84687791"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com o gateway virtual SKUs (SKUs legado)

Este artigo contém informações sobre o legado (antigo) gateway virtual gateway SKUs. O legado SKUs ainda funciona em ambos os modelos de implementação para gateways VPN que já foram criados. As portas clássicas da VPN continuam a usar o legado SKUs, tanto para os gateways existentes, como para novos gateways. Ao criar novos gateways VPN do Resource Manager, utilize os novos SKUs de gateway. Para obter informações sobre os novos SKUs, consulte [Sobre o Gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Pode ver os preços de gateway legado na secção **Virtual Network Gateways,** que está localizada na [página de preços expressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Débito agregado estimado por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Configurações suportadas por tipo SKU e VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Redimensione uma porta de entrada

Você pode redimensionar a sua porta de entrada para um gateway SKU dentro da mesma família SKU. Por exemplo, se tiver um SKU Standard, pode redimensionar para um SKU highPerformance. No entanto, não pode redimensionar a sua porta de entrada VPN entre os antigos SKUs e as novas famílias SKU. Por exemplo, não pode ir de um SKU Standard para um SKU VpnGw2, ou um SKU Básico para VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Para redimensionar um gateway para o modelo de implementação do Gestor de Recursos utilizando o PowerShell, utilize o seguinte comando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Também pode redimensionar uma porta de entrada no portal Azure.

### <a name="classic"></a><a name="classicresize"></a>Clássico

Para redimensionar uma porta de entrada para o modelo de implementação clássico, tem de utilizar os cmdlets PowerShell de Gestão de Serviço. Utilize o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Alteração para o novo gateway SKUs

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre os novos SKUs gateway, consulte [gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku).

Para obter mais informações sobre as definições de configuração, consulte [as definições de configuração do Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).
