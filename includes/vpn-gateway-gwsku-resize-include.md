---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73495760"
---
Pode utilizar o `Resize-AzVirtualNetworkGateway` cmdlet PowerShell para atualizar ou desclassificar um SKU De Geração1 ou Geração2 (todos os SKUs VpnGw podem ser redimensionados, exceto SKUs Básicos). Se estiver a utilizar o Gateway Basic SKU, [utilize estas instruções para](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) redimensionar o seu gateway.

O exemplo seguinte da PowerShell mostra um gateway SKU a ser redimensionado para VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Também pode redimensionar um gateway no portal Azure, indo para a página **configuração** para o seu gateway de rede virtual e selecionando um SKU diferente a partir do dropdown.
