---
title: Configure respostas personalizadas para firewall de aplicação web (WAF) com Azure Front Door
description: Saiba como configurar um código de resposta personalizado e uma mensagem quando a WAF bloqueia um pedido.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563686"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Configure uma resposta personalizada para a Firewall de Aplicação Web Azure (WAF)

Por predefinição, quando a WAF bloqueia um pedido por causa de uma regra compatível, devolve um código de estado 403 com a mensagem **bloqueada.** A mensagem predefinida também inclui o string de referência de rastreio que pode ser usado para ligar para [registar entradas](./waf-front-door-monitor.md) para o pedido.  Pode configurar um código de estado de resposta personalizado e uma mensagem personalizada com cadeia de referência para o seu caso de utilização. Este artigo descreve como configurar uma página de resposta personalizada quando um pedido é bloqueado pela WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Configurar código de estado de resposta personalizada e portal de utilização de mensagens

Pode configurar um código de estado de resposta personalizado e um organismo em "Definições de política" a partir do portal WAF.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="Definições de política da WAF":::

No exemplo acima, mantivemos o código de resposta como 403, e configuramos uma mensagem curta "Please contact us", como mostra a imagem abaixo:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Exemplo de resposta personalizada":::

"{{azure-ref}}" insere a cadeia de referência única no corpo de resposta. O valor corresponde ao campo TrackingReference nos `FrontdoorAccessLog` registos e `FrontdoorWebApplicationFirewallLog` registos.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>Configure código de estado de resposta personalizada e utilização de mensagem PowerShell

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](/powershell/azure/) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sômposições com as suas credenciais Azure e instale o módulo Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue ao Azure com um diálogo interativo para o sent-in

```
Connect-AzAccount
Install-Module -Name Az

```
Certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalar módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Aqui criamos um grupo de recursos utilizando [o New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Abaixo está um exemplo de criação de uma nova política da WAF com código de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) estado de resposta personalizada definido para 405, e a mensagem para **You está bloqueada.**

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modificar as definições do código de resposta personalizada ou das configurações do corpo de resposta de uma política waf existente, utilizando [a Actualização-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [firewall de aplicação web com porta frontal Azure](../afds/afds-overview.md)