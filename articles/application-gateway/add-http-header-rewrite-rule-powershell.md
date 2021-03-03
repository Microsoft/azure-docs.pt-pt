---
title: Reescrever cabeçalhos HTTP em Gateway de Aplicação Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP em Azure Application Gateway usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711693"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescrever os cabeçalhos de pedido e resposta HTTP com Gateway de Aplicação Azure - Azure PowerShell

Este artigo descreve como usar a Azure PowerShell para configurar uma [instância SKU do Gateway v2 para](./application-gateway-autoscaling-zone-redundant.md) reescrever os cabeçalhos HTTP em pedidos e respostas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Você precisa executar Azure PowerShell localmente para completar os passos neste artigo. Também precisa de ter a versão 1.0.0 do módulo Az ou posteriormente instalada. Corra `Import-Module Az` e, em seguida, `Get-Module Az` determine a versão que instalou. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.
- Precisa de ter uma instância SKU do Gateway v2. Reescrevendo cabeçalhos não é suportado no V1 SKU. Se não tiver o V2 SKU, crie uma [instância SKU SKU do Gateway v2](./tutorial-autoscale-ps.md) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **RequerimentoConfiguration**: Usado para especificar os campos de cabeçalho de pedido que pretende reescrever e o novo valor para os cabeçalhos.

   - **RespostaConfiguration**: Usado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor para os cabeçalhos.

   - **ActionSet**: Contém as configurações dos cabeçalhos de pedido e resposta especificados anteriormente.

   - **Condição**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas HTTP(S). A ação de reescrita ocorrerá se o pedido http(S) ou a resposta corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições estão reunidas. Por outras palavras, a operação é uma operação lógica.

   - **ReescritaRule**: Contém combinações de condições de reescrita múltiplas/ reescrita.

   - **RegraSSequence**: Uma configuração opcional que ajuda a determinar a ordem em que as regras de reescrita executam. Esta configuração é útil quando tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras mais baixo corre primeiro. Se atribuir o mesmo valor de sequência de regras a duas regras de reescrita, a ordem de execução não é determinística.

     Se não especificar explicitamente a regra, é definido um valor padrão de 100.

   - **ReescreveruleSet**: Contém várias regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe o RewriteRuleSet a uma regra de encaminhamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de encaminhamento. Quando utiliza uma regra de encaminhamento básico, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho URL. Nesse caso, aplica-se apenas à área específica do percurso de um sítio.

Pode criar vários conjuntos de reescrita do cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas pode aplicar apenas um conjunto de reescrita a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especificar a configuração da regra de reescrita do cabeçalho HTTP

Neste exemplo, modificaremos um URL de reorientação reescrevendo o cabeçalho de localização na resposta HTTP sempre que o cabeçalho de localização contiver uma referência a azurewebsites.net. Para isso, adicionaremos uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net. Usaremos o `(https?)://.*azurewebsites.net(.*)$` padrão. E usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como valor de cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Recupere a configuração do seu gateway de aplicação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recupere a configuração da sua regra de encaminhamento de pedidos

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicações com a configuração para reescrever cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Excluir uma regra de reescrita

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns de reescrita do cabeçalho](./rewrite-http-headers.md).