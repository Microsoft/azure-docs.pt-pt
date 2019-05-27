---
title: 'Repor um circuito com falhas - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo ajuda-o a repor um circuito do ExpressRoute que está no estado de falha.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f6fd8e5f0e5730e55fa5bbe13f74be796dd6a4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115845"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Repor um circuito do ExpressRoute com falhas

Quando uma operação de um circuito do ExpressRoute não for concluída com êxito, o circuito pode entrar no estado de "Falha". Este artigo ajuda-o a repor um circuito do ExpressRoute do Azure com falhas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>Repor um circuito

1. Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, veja [nstall and configure Azure PowerShell (Instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

2. Abra a consola do PowerShell com privilégios elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Especifique a subscrição que pretende utilizar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Execute os seguintes comandos para repor um circuito que está no Estado com falhas:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

O circuito deve agora ser bom estado de funcionamento. Abra um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se o circuito ainda está em estado de falha.

## <a name="next-steps"></a>Passos Seguintes

Abra um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda ocorrerem problemas.
