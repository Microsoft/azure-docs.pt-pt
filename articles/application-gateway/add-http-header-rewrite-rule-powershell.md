---
title: Cabeçalhos HTTP de reescrita no Gateway de aplicação do Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP no Gateway de aplicação do Azure com o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947194"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicação do Azure - Azure PowerShell

Este artigo descreve como utilizar o Azure PowerShell para configurar uma [SKU do Gateway de aplicação v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instância reescreva os cabeçalhos HTTP em solicitações e respostas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Terá de executar o PowerShell do Azure localmente para concluir os passos neste artigo. Também tem de ter Az versão 1.0.0 do módulo ou posterior instalado. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para determinar a versão que instalou. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.
- Tem de ter uma instância SKU do Gateway de aplicação v2. Reescrever cabeçalhos não é suportada no SKU do v1. Se não tiver o SKU de v2, crie uma [SKU do Gateway de aplicação v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instância antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita de cabeçalho HTTP, terá de concluir estes passos.

1. Crie os objetos que são necessários para a reescrita de cabeçalho HTTP:

   - **RequestHeaderConfiguration**: Utilizado para especificar os campos de cabeçalho de solicitação que pretende reescrever e o novo valor para os cabeçalhos.

   - **ResponseHeaderConfiguration**: Utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor para os cabeçalhos.

   - **ActionSet**: Contém as configurações dos cabeçalhos de solicitação e resposta especificados anteriormente.

   - **Condição**: Uma configuração opcional. Condições de reescrita avaliam o conteúdo dos pedidos de HTTP (S) e as respostas. A ação de reescrita ocorrerá se o pedido de HTTP (S) ou resposta corresponde a condição de regravação.

     Se associar mais do que uma condição uma ação, a ação ocorre apenas quando todas as condições são cumpridas. Em outras palavras, a operação é uma operação lógica AND.

   - **RewriteRule**: Contém vários de reescrita de ação / reescrever combinações de condição.

   - **RuleSequence**: Uma configuração opcional que ajuda a determinar a ordem em que regras de reescrita de execução. Esta configuração é útil quando tem várias regras de reescrita de um conjunto de regravação. Uma regra de reescrita que tem um valor de seqüência de regra inferior é executado primeira. Se atribuir o mesmo valor de seqüência de regra para duas regras de reescrita, a ordem de execução é determinística.

     Se não especificar explicitamente o RuleSequence, está definido um valor predefinido de 100.

   - **RewriteRuleSet**: Contém várias regras de reescrita que vão ser associadas a uma regra de encaminhamento do pedido.

2. Anexe o RewriteRuleSet a uma regra de roteamento. A configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utiliza uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando utiliza uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Nesse caso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar a cada reescrita definida como vários serviços de escuta. Mas pode aplicar-se apenas um reescrever definido como um serviço de escuta específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especifique a configuração de regra de reescrever o cabeçalho HTTP

Neste exemplo, vamos modificar um URL de redirecionamento reescrevendo o cabeçalho de localização na resposta HTTP, sempre que o cabeçalho location contém uma referência a azurewebsites.net. Para fazer isso, vamos adicionar uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net. Vamos utilizar o padrão `(https?):\/\/.*azurewebsites\.net(.*)$`. E vamos usar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho. Este valor irá substituir *azurewebsites.net* com *contoso.com* no cabeçalho location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Obter a configuração do seu gateway de aplicação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Obter a configuração da sua regra de encaminhamento de pedido

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicação com a configuração de regravação de cabeçalhos HTTP

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

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como configurar alguns casos de utilização comuns, consulte [cabeçalho comum reescrever cenários](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).