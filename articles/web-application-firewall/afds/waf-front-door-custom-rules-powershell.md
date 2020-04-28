---
title: Configure regras personalizadas da WAF & definido de regra padrão para porta frontal azure
description: Aprenda a configurar uma política waf consistindo em regras personalizadas e geridas para um ponto final da Porta da Frente existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74186639"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configure uma política de firewall de aplicação web usando o Azure PowerShell

A política azure Web Application Firewall (WAF) define as inspeções necessárias quando um pedido chega à Porta da Frente.
Este artigo mostra como configurar uma política waf que consiste em algumas regras personalizadas e com o Conjunto de Regras padrão gerido pelo Azure ativado.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar uma política de limite de taxas, crie o seu ambiente PowerShell e crie um perfil front door.

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```
Connect-AzAccount

```
Antes de instalar o módulo do Front Door, certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instale módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil porta da frente

Crie um perfil porta da frente seguindo as instruções descritas em [Quickstart: Criar um perfil porta da frente](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada baseada em parâmetros http

O exemplo seguinte mostra como configurar uma regra personalizada com duas condições de jogo usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Os pedidos são de um site especificado, tal como definido pelo remetente, e a cadeia de consulta não contém "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada com base no método de pedido de http

Crie uma regra que bloqueie o método "PUT" utilizando [o New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) da seguinte forma:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Criar uma regra personalizada com base na restrição de tamanho

O exemplo seguinte cria uma regra bloqueando pedidos com Url que é mais de 100 caracteres usando Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar conjunto de regras de padrão gerido

O exemplo seguinte cria um conjunto de regras de padrão gerido utilizando o Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontre o nome do grupo de recursos `Get-AzResourceGroup`que contém o perfil porta da frente utilizando . Em seguida, configure uma política de segurança com regras criadas nos passos anteriores usando [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil porta da frente.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Link política para um anfitrião frontal da porta da frente

Ligue o objeto de política de segurança a um anfitrião frontal existente e atualize as propriedades da Porta da Frente. Primeiro, recupere o objeto da porta da frente usando [get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, detete a propriedade *frontal WebApplicationFirewallPolicyLink* para o *recurso Id* do "$myWAFPolicy$" criado no passo anterior utilizando [o Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil porta da frente usando instruções fornecidas no [Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md) Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil porta da frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Só precisa de definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para ligar uma política de segurança a uma frente frontal da Porta Da Frente. As atualizações de políticas subsequentes são automaticamente aplicadas na parte frontal.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente](../../frontdoor/front-door-overview.md) 
- Saiba mais sobre [WAF com porta da frente](afds-overview.md)
