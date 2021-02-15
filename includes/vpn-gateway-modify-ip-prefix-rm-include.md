---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7dd255e9767309c7b273dccfa0ee5675eed18568
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380503"
---
Para adicionar prefixos de endereços adicionais:

1. Defina a variável para o LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Modifique os prefixos.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

Para remover um prefixos de endereço:

  Não inclua os prefixos que já não são necessários. Neste exemplo, já não precisamos do prefixo 10.101.2.0/24 (do exemplo anterior), pelo que atualizamos o gateway de rede local, excluindo o prefixo.

1. Defina a variável para o LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Desacorra o portal com os prefixos atualizados.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
