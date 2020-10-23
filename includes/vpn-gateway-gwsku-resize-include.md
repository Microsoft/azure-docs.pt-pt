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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424330"
---
Pode utilizar o `Resize-AzVirtualNetworkGateway` cmdlet PowerShell para atualizar ou desclassificar um SKU De Geração1 ou Geração2 (todos os SKUs VpnGw podem ser redimensionados, exceto SKUs Básicos). Se estiver a utilizar o Gateway Basic SKU, [utilize estas instruções para](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) redimensionar o seu gateway.

O exemplo seguinte da PowerShell mostra um gateway SKU a ser redimensionado para VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```