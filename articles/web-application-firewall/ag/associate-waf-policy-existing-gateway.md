---
title: Associar uma política de firewall de aplicação web com um gateway de aplicação azure existente
description: Saiba como associar uma política de Firewall de aplicação web com um Gateway de Aplicação Azure existente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74083909"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associar uma política waf com um gateway de aplicação existente

Pode utilizar o Azure PowerShell para [criar uma Política WAF,](create-waf-policy-ag.md)mas pode já ter um Gateway de Aplicação e apenas querer associar-lhe uma Política WAF. Neste artigo, fazes isso mesmo; cria-se uma Política WAF e associa-a a um Gateway de Aplicação já existente. 

1. Obtenha o seu Gateway de Aplicação e a política de firewall. Se não tiver uma política de firewall existente, consulte o passo 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Opcional) Crie uma Política de Firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se está a criar esta Política waf para transitar de um WAF Config para uma Política WAF, então a Política tem de ser uma cópia exata do seu antigo Config. Isto significa que todas as exclusões, regras personalizadas, grupo de regras deficientes, etc. têm de ser exatamente as mesmas que no WAF Config.
3. (Opcional) Pode configurar a política waf de acordo com as suas necessidades. Isto inclui regras personalizadas, desativação de regras/grupos de regras, exclusões, definição de limites de upload de ficheiros, etc. Se saltar este passo, serão selecionados todos os incumprimentos. 
   
4. Guarde a apólice e prenda-a ao seu Gateway de Aplicação. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre regras personalizadas.](configure-waf-custom-rules.md)
