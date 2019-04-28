---
title: Configurar uma resposta personalizada para o firewall de aplicação web na porta da frente do Azure
description: Saiba como configurar um código de resposta personalizada e uma mensagem quando a firewall de aplicações web (WAF) bloquear um pedido.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459781"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Configurar uma resposta personalizada para o firewall de aplicação web do Azure

Por predefinição, quando a firewall de aplicações web do Azure (WAF) porta da frente com o Azure bloqueia um pedido devido a uma regra correspondente, ele retorna um código de 403 status com **o pedido está bloqueado** mensagem. Este artigo descreve como configurar um código de estado de resposta personalizada e uma mensagem de resposta quando um pedido está bloqueado pela WAF.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as credenciais do Azure e instale o módulo do PowerShell de Az.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligar ao Azure com uma caixa de diálogo de início de sessão interativa
```
Connect-AzAccount
Install-Module -Name Az
```
Certifique-se de que tem a versão atual do PowerShellGet instalado. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalar o módulo de Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, alocar recursos relacionados a um grupo de recursos. Neste exemplo, crie um grupo de recursos, utilizando [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Criar uma nova política de WAF com resposta personalizada 

Segue-se um exemplo de como criar uma nova política de WAF com o código de estado de resposta personalizada definido como 405 e a mensagem para **estão a ser bloqueados.** usando [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Modificar o código de resposta personalizada ou definições de corpo de resposta de uma política de WAF existente, utilizando [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [porta de entrada](front-door-overview.md)