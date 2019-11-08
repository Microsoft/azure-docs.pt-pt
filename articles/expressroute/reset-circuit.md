---
title: 'Redefinir um circuito com falha-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo ajuda você a redefinir um circuito do ExpressRoute que está em um estado de falha.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748104"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Redefinir um circuito de ExpressRoute com falha

Quando uma operação em um circuito do ExpressRoute não é concluída com êxito, o circuito pode entrar em um estado de ' falha '. Este artigo ajuda você a redefinir um circuito do Azure ExpressRoute com falha.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Redefinir um circuito

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
5. Execute os seguintes comandos para redefinir um circuito que está em um estado de falha:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

O circuito agora deve estar íntegro. Abra um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se o circuito ainda estiver em um estado de falha.

## <a name="next-steps"></a>Passos seguintes

Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.
