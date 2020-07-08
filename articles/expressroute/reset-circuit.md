---
title: 'Repor um circuito falhado - ExpressRoute: PowerShell: Azure / Microsoft Docs'
description: Este artigo ajuda-o a redefinir um circuito ExpressRoute que está num estado falhado.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f75d9ae2fc788bbeb769aaf412c77dfacec9d04c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738129"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Reset a failed ExpressRoute circuit (Repor circuitos do ExpressRoute com falha)

Quando uma operação num circuito ExpressRoute não estiver concluída com sucesso, o circuito pode entrar num estado "falhado". Este artigo ajuda-o a redefinir um circuito Azure ExpressRoute falhado.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Reiniciar um circuito

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
5. Executar os seguintes comandos para reiniciar um circuito que está em estado de falha:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

O circuito deve agora estar saudável. Abra um bilhete de suporte com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se o circuito ainda estiver em estado de falha.

## <a name="next-steps"></a>Próximos passos

Abra um bilhete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda estiver com problemas.
