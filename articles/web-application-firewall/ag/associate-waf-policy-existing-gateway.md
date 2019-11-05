---
title: Associar uma política de firewall do aplicativo Web a um gateway de Aplicativo Azure existente
description: Saiba como associar uma política de firewall do aplicativo Web a um gateway de Aplicativo Azure existente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: b455849c889c463fbda305e690f998d58fab0d8f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516880"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associar uma política de WAF a um gateway de aplicativo existente

Você pode usar Azure PowerShell para [criar uma política de WAF](create-waf-policy-ag.md), mas talvez você já tenha um gateway de aplicativo e queira apenas associar uma política de WAF a ele. Neste artigo, você faz exatamente isso; você cria uma política de WAF e a associa a um gateway de aplicativo já existente. 

1. Obtenha o gateway de aplicativo e a política de firewall. Se você não tiver uma política de firewall existente, consulte a etapa 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Adicional Criar uma política de firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se você estiver criando essa política de WAF para fazer a transição de uma configuração de WAF para uma política de WAF, a política precisará ser uma cópia exata de sua configuração antiga. Isso significa que cada exclusão, regra personalizada, grupo de regras desabilitado, etc., precisa ser exatamente o mesmo que está na configuração de WAF.
3. Adicional Você pode configurar a política de WAF para atender às suas necessidades. Isso inclui regras personalizadas, desabilitando regras/grupos de regra, exclusões, definindo limites de carregamento de arquivo, etc. Se você ignorar essa etapa, todos os padrões serão selecionados. 
   
4. Salve a política e anexe-a ao seu gateway de aplicativo. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre regras personalizadas.](/configure-waf-custom-rules.md)
