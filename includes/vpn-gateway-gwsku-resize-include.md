---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908557"
---
Para as SKUs atuais (VpnGw1, VpnGw2 e VPNGW3) que deseja redimensionar o gateway de SKU para atualizar para um mais poderoso, pode usar o `Resize-AzVirtualNetworkGateway` cmdlet do PowerShell. Também podem passar o utilizar este cmdlet de tamanho SKU de gateway. Se estiver a utilizar o SKU, de gateway básico [em alternativa, utilize estas instruções](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) para redimensionar o gateway.

O exemplo de PowerShell seguinte mostra um redimensionada para VpnGw2 SKU de gateway.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Também pode redimensionar um gateway no portal do Azure ao aceder a **configuração** página para o seu gateway de rede virtual e selecionar um SKU de diferente na lista pendente.