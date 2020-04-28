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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73495760"
---
Pode utilizar `Resize-AzVirtualNetworkGateway` o cmdlet PowerShell para atualizar ou desvalorizar uma Geração1 ou Generation2 SKU (todas as VpnGw SKUs podem ser redimensionadas exceto SKUs Básicos). Se estiver a utilizar o Gateway Básico SKU, [utilize estas instruções](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) para redimensionar o seu portal.

O exemplo seguinte da PowerShell mostra um Portal SKU a ser redimensionado para VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Também pode redimensionar um portal no portal Azure indo para a página de **Configuração** para o seu gateway de rede virtual e selecionando um SKU diferente do dropdown.
