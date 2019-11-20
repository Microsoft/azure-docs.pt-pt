---
title: Configurar uma resposta personalizada para o WAF com a porta frontal do Azure
description: Saiba como configurar um código de resposta personalizado e uma mensagem quando o WAF (firewall do aplicativo Web) bloqueia uma solicitação.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185353"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configurar uma resposta personalizada para o Firewall do aplicativo Web do Azure

Por padrão, quando o WAF (firewall do aplicativo Web) do Azure com a porta frontal do Azure bloqueia uma solicitação devido a uma regra correspondente, ele retorna um código de status 403 com **a solicitação é mensagem bloqueada** . Este artigo descreve como configurar um código de status de resposta personalizado e uma mensagem de resposta quando uma solicitação é bloqueada pelo WAF.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instalar o módulo AZ PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conectar-se ao Azure com uma caixa de diálogo interativa para entrar
```
Connect-AzAccount
Install-Module -Name Az
```
Verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalar o módulo AZ. FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você aloca recursos relacionados a um grupo de recursos. Neste exemplo, você cria um grupo de recursos usando [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Abaixo está um exemplo de como criar uma nova política WAF com o código de status de resposta personalizado definido como 405 e a mensagem para **você está bloqueada.** usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Modifique as configurações do código de resposta personalizado ou do corpo da resposta de uma política WAF existente, usando [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
- Saiba mais sobre o [Firewall do aplicativo Web com a porta frontal do Azure](../afds/afds-overview.md)