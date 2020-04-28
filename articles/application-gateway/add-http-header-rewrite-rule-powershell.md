---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicação Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP no Portal de Aplicação Azure, utilizando o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "64947194"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescrever cabeçalhos de pedido e resposta HTTP com Gateway de Aplicação Azure - Azure PowerShell

Este artigo descreve como usar o Azure PowerShell para configurar uma instância [de Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) para reescrever os cabeçalhos HTTP em pedidos e respostas.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Você precisa executar Azure PowerShell localmente para completar os passos deste artigo. Também precisa de ter o módulo Az versão 1.0.0 ou posteriormente instalado. Executar `Import-Module Az` e, em seguida, `Get-Module Az` determinar a versão que instalou. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.
- Precisa de ter uma instância de Entrada de Aplicação v2 SKU. Reescrever cabeçalhos não é suportado no V1 SKU. Se não tiver o V2 SKU, crie uma instância [SKU De Gateway v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Criar os objetos necessários para reescrever o cabeçalho HTTP:

   - **RequestHeaderConfiguration**: Utilizado para especificar os campos de cabeçalho de pedido que pretende reescrever e o novo valor para os cabeçalhos.

   - **Configuração do Cabeçalho**de Resposta : Utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor para os cabeçalhos.

   - **ActionSet**: Contém as configurações dos cabeçalhos de pedido e resposta especificados anteriormente.

   - **Condição**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas http(S). A ação de reescrita ocorrerá se o pedido ou resposta HTTP(S) corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições são satisfeitas. Por outras palavras, a operação é uma operação lógica e operacional.

   - **ReescreverRegra**: Contém múltiplas combinações de condições de reescrita/ reescrita.

   - **RuleSequence**: Uma configuração opcional que ajuda a determinar a ordem em que as regras de reescrita executam. Esta configuração é útil quando se tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras inferior corre primeiro. Se atribuir o mesmo valor de sequência de regras a duas regras de reescrita, a ordem de execução não é determinista.

     Se não especificar explicitamente a Sequência de Regras, é definido um valor predefinido de 100.

   - **RewriteRuleSet**: Contém múltiplas regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe o RewriteRuleSet a uma regra de encaminhamento. A configuração de reescrita é fixada ao ouvinte de origem através da regra de encaminhamento. Quando se utiliza uma regra básica de encaminhamento, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho do URL. Nesse caso, aplica-se apenas à área específica do caminho de um local.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas só pode aplicar um conjunto de reescrita a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especifique a configuração da regra de reescrita do cabeçalho HTTP

Neste exemplo, modificaremos um URL de reorientação reescrevendo o cabeçalho de localização na resposta HTTP sempre que o cabeçalho de localização contenha uma referência a azurewebsites.net. Para isso, adicionaremos uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net. Usaremos o padrão. `(https?):\/\/.*azurewebsites\.net(.*)$` E usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como valor de cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

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

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualize o gateway da aplicação com a configuração para reescrever os cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Eliminar uma regra de reescrita

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)de reescrita do cabeçalho .