---
title: Associar uma política de Firewall de aplicação web com um Gateway de aplicação Azure existente
description: Saiba como associar uma política de Firewall de aplicação web a um Gateway de aplicação Azure existente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74083909"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associar uma política da WAF a um Gateway de aplicação existente

Pode utilizar a Azure PowerShell para [criar uma Política WAF,](create-waf-policy-ag.md)mas pode já ter um Gateway de Aplicação e apenas querer associar-lhe uma Política WAF. Neste artigo, faz-se isso mesmo; cria uma Política WAF e associa-a a um Gateway de aplicação já existente. 

1. Obtenha a sua Política de Gateway de Aplicação e Firewall. Se não tiver uma política de firewall existente, consulte o passo 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Opcional) Criar uma política de firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se está a criar esta Política WAF para a transição de uma WAF Config para uma Política WAF, então a Política tem de ser uma cópia exata do seu antigo Config. Isto significa que todas as exclusões, regras personalizadas, grupo de regras desativadas, etc. têm de ser exatamente iguais às do WAF Config.
3. (Opcional) Pode configurar a política da WAF de acordo com as suas necessidades. Isto inclui regras personalizadas, desativação de regras/grupos de regras, exclusões, definição de limites de upload de ficheiros, etc. Se saltar este passo, todos os predefinidos serão selecionados. 
   
4. Guarde a apólice e anexe-a ao seu Gateway de Aplicação. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as Regras Personalizadas.](configure-waf-custom-rules.md)
