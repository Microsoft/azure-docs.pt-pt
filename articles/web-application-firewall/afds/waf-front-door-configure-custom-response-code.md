---
title: Configure uma resposta personalizada para WAF com porta frontal azure
description: Saiba como configurar um código e mensagem de resposta personalizados quando o Firewall de aplicação web (WAF) bloqueia um pedido.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185353"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configure uma resposta personalizada para firewall de aplicação web azure

Por predefinição, quando o Azure Web Application Firewall (WAF) com a Porta Frontal Azure bloqueia um pedido por causa de uma regra correspondida, devolve um código de estado 403 com o pedido é uma mensagem **bloqueada.** Este artigo descreve como configurar um código de estado de resposta personalizado e mensagem de resposta quando um pedido é bloqueado por WAF.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as suas credenciais Azure e instale o módulo Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue-se a Azure com um diálogo interativo para iniciar sessão
```
Connect-AzAccount
Install-Module -Name Az
```
Certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instale módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, você aloca recursos relacionados a um grupo de recursos. Neste exemplo, cria-se um grupo de recursos utilizando o [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Abaixo está um exemplo de criação de uma nova política waf com código de estado de resposta personalizado definido para 405 e mensagem para **Você está bloqueada.** usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Modifique as definições personalizadas de código de resposta ou do corpo de resposta de uma política WAF existente, utilizando [a Atualização AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre firewall de [aplicação web com porta frontal Azure](../afds/afds-overview.md)