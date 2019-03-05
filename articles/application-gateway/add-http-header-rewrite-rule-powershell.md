---
title: Reescreva os cabeçalhos HTTP num Gateway de aplicação do Azure existente
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP num Gateway de aplicação do Azure existente com o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: f3bda2cf680fa1ad17b5cbb3b9ba5911c78a1e4e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316511"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Reescreva os cabeçalhos HTTP num gateway de aplicação existente

Pode utilizar o Azure PowerShell para configurar [regras de reescrita de cabeçalhos de solicitação e resposta HTTP](rewrite-http-headers.md) num existente [dimensionamento automático e o SKU de gateway de aplicação com redundância de zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT]
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Obter a configuração de um gateway de aplicação existente
> * Especifique a configuração de regra de reescrita de cabeçalho de http
> * Atualizar o gateway de aplicação com a configuração acima para reescrever os cabeçalhos de http

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige que execute o Azure PowerShell localmente. Tem de ter Az versão 1.0.0 do módulo ou posterior instalado. Execute `Import-Module Az` e, em seguida,`Get-Module Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Especifique a configuração de regra de reescrita de cabeçalho de http**

Configure os novos objetos necessários para reescreva os cabeçalhos de http:

- **RequestHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
- **ResponseHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
- **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e resposta especificados acima.
- **RewriteRule**: este objeto contém todos os *actionSets* especificado acima.
- **RewriteRuleSet**-este objeto contém todos os *rewriteRules* e tem de ser vinculado a uma regra de encaminhamento para a solicitação como-básica ou baseado no caminho.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Obter a configuração do seu gateway de aplicação existente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Obter a configuração da sua regra de encaminhamento de pedido existente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicação com a configuração de regravação de cabeçalhos de http

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Eliminar uma regra de reescrita

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um gateway de aplicação com regras de encaminhamento com base no caminho de URL](./tutorial-url-route-powershell.md)
