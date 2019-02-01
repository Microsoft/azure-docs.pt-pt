---
title: Rede virtual do Azure SKUs de gateway VPN legado | Documentos da Microsoft
description: Como trabalhar com o gateway de rede virtual antigo SKUs; Básico, padrão e HighPerformance.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: efce8379ecafe6e8e044b654a3c5b392ca8e9cea
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506367"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com o gateway de rede virtual SKUs (SKUs legados)

Este artigo contém informações sobre o gateway de rede virtual (antigos) SKUs legados. O legacy SKUs continuam a funcionar em ambos os modelos de implementação para gateways VPN que já foram criadas. Gateways de VPN clássicos continuam a utilizar SKUs legados, tanto para os gateways existentes e para novos gateways. Quando criar gateways de VPN de Gestor de recursos novo, utilize os novos SKU de gateway. Para obter informações sobre os novos SKU, consulte [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Débito agregado estimado por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurações suportadas por tipo de SKU e VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionar um gateway

Pode redimensionar o gateway de um SKU de gateway dentro da mesma família SKU. Por exemplo, se tiver um SKU Standard, pode redimensionar para um SKU HighPerformance. No entanto, não pode redimensionar o gateway de VPN entre os SKU antigos e as novas famílias SKU. Por exemplo, não pode ir de um SKU Standard para um SKU de VpnGw2 ou para uma SKU básico para VpnGw1.

Para redimensionar um gateway para o modelo de implementação clássica, utilize o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Para redimensionar um gateway para o modelo de implementação do Resource Manager com o PowerShell, utilize o seguinte comando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Também pode redimensionar um gateway no portal do Azure.

## <a name="change"></a>Alterar para o novo gateway SKUs

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os novos SKU de Gateway, consulte [SKUs de Gateway](vpn-gateway-about-vpngateways.md#gwsku).

Para obter mais informações sobre as definições de configuração, consulte [definições de configuração do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).