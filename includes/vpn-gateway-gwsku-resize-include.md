---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495760"
---
Você pode usar o cmdlet `Resize-AzVirtualNetworkGateway` PowerShell para atualizar ou fazer downgrade de um SKU Generation1 ou Generation2 (todas as SKUs VpnGw podem ser redimensionadas, exceto SKUs básicos). Se você estiver usando o SKU do gateway básico, [Use estas instruções em vez](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) de redimensionar o gateway.

O exemplo do PowerShell a seguir mostra um SKU de gateway sendo redimensionado para VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Você também pode redimensionar um gateway na portal do Azure acessando a página de **configuração** do seu gateway de rede virtual e selecionando uma SKU diferente na lista suspensa.
