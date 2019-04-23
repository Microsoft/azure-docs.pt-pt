---
title: Cabeçalhos HTTP de reescrita no Gateway de aplicação do Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP no Gateway de aplicação do Azure com o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005626"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicação do Azure - Azure PowerShell

Este artigo mostra-lhe como utilizar o Azure PowerShell para configurar uma [SKU do Gateway de aplicação v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) reescreva os cabeçalhos HTTP nas solicitações e respostas.

> [!IMPORTANT]
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Este tutorial exige que execute o Azure PowerShell localmente. Tem de ter Az versão 1.0.0 do módulo ou posterior instalado. Execute `Import-Module Az` e, em seguida,`Get-Module Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.
- Tem de ter um v2 de Gateway de aplicação SKU, uma vez que a capacidade de Reescrita do cabeçalho não é suportado para o SKU de v1. Se não tiver o SKU de v2, crie uma [SKU do Gateway de aplicação v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="what-is-required-to-rewrite-a-header"></a>O que é necessário reescrever um cabeçalho

Para configurar a reescrita de cabeçalho HTTP, precisará para:

1. Crie novos objetos necessários para reescreva os cabeçalhos de http:

   - **RequestHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.

   - **ResponseHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.

   - **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e resposta especificados acima.

   - **Condição**: É uma configuração opcional. Se for adicionada uma condição de reescrita, avaliará o conteúdo dos pedidos de HTTP (S) e as respostas. A decisão para executar a ação de reescrita associada com a condição de reescrita irão basear-se a solicitação de HTTP (S) ou resposta correspondidos com a condição de regravação. 

     Se mais do que um condições estão associados com uma ação, em seguida, a ação será executado apenas quando todas as condições são cumpridas, ou seja, uma operação lógica AND será efetuada.

   - **RewriteRule**: contém vários de reescrita de ação - combinações de condição de reescrita.

   - **RuleSequence**: Esta é uma configuração opcional. Ele ajuda a determinar a ordem em que são executadas as regras de reescrita de diferentes. Isso é útil quando existem várias regras de reescrita de um conjunto de regravação. A regra de reescrita com o menor valor de seqüência de regra é executada pela primeira vez. Se fornecer a mesma seqüência de regra para duas regras de reescrita será determinística, em seguida, a ordem de execução.

     Se não especificar explicitamente o RuleSequence, definirá um valor predefinido de 100.

   - **RewriteRuleSet**: este objeto contém várias regras de reescrita que serão associadas a uma regra de encaminhamento do pedido.

2. Será solicitado para anexar o rewriteRuleSet com uma regra de roteamento. Isto acontece porque a configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utilizar uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando é utilizada uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho de http e cada conjunto de reescrita pode ser aplicado a vários serviços de escuta. No entanto, pode aplicar apenas um reescrever definido como um serviço de escuta específico.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Especifique a configuração de regra de reescrita de cabeçalho de http**

Neste exemplo, modificaremos o URL de redirecionamento reescrevendo o cabeçalho de localização na resposta http, sempre que o cabeçalho location contém uma referência a "azurewebsites.net". Para fazer isso, vamos adicionar uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net ao utilizar o padrão `(https?):\/\/.*azurewebsites\.net(.*)$`. Nós usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho. Esta ação irá substituir *azurewebsites.net* com *contoso.com* no cabeçalho location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Obter a configuração do seu gateway de aplicação existente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Obter a configuração da sua regra de encaminhamento de pedido existente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicação com a configuração de regravação de cabeçalhos de http

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

Para saber mais sobre a configuração necessária para realizar algumas do comum casos de utilização, veja [cabeçalho comum reescrever cenários](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).