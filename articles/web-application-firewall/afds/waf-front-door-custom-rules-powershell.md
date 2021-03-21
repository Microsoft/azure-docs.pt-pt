---
title: Configurar regras personalizadas waf & regra padrão definida para a porta frontal azul
description: Saiba como configurar uma política da WAF composta por regras personalizadas e geridas para um ponto final da Porta Frontal existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 9a0e262db9f5c37189a589eefc451a88dd5ea8c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563414"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configure uma política de Firewall de aplicação web usando a Azure PowerShell

A política Azure Web Application Firewall (WAF) define as inspeções necessárias quando um pedido chega à Porta da Frente.
Este artigo mostra como configurar uma política da WAF que consiste em algumas regras personalizadas e com o Conjunto de Regras Padrão gerido pelo Azure ativado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar uma política de limite de taxa, crie o seu ambiente PowerShell e crie um perfil da Porta Frontal.

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](/powershell/azure/) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sômposições com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```
Connect-AzAccount

```
Antes de instalar o módulo do Front Door, certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil da porta da frente

Crie um perfil da porta frontal seguindo as instruções descritas no [Quickstart: Criar um perfil da porta frontal](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada baseada em parâmetros http

O exemplo a seguir mostra como configurar uma regra personalizada com duas condições de jogo usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Os pedidos são de um site especificado, conforme definido pelo remetente, e o string de consulta não contém "palavra-passe". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada baseada no método de pedido de http

Crie uma regra que bloqueie o método "PUT" utilizando [o new-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) da seguinte forma:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Criar uma regra personalizada baseada na restrição de tamanho

O exemplo a seguir cria uma regra que bloqueia pedidos com Url que é superior a 100 caracteres usando Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar conjunto de regras de incumprimento gerido

O exemplo a seguir cria um Conjunto de Regras Padrão gerido utilizando Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configure uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil da porta frontal utilizando `Get-AzResourceGroup` . Em seguida, configurar uma política de segurança com regras criadas nos passos anteriores usando [a New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil da porta frontal.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de ligação a um anfitrião frontal da porta da frente

Ligue o objeto da política de segurança a um anfitrião frontal existente e atualize as propriedades da Porta Frontal. Em primeiro lugar, recupere o objeto da porta frontal utilizando [o Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, deite a propriedade *front-end WebApplicationFirewallPolicyLink* para os *recursosId* do "$myWAFPolicy$" criado no passo anterior usando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil da Porta Frontal usando instruções fornecidas no [Quickstart: Criar um](../../frontdoor/quickstart-create-front-door.md) artigo da Porta Frontal. Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil da Porta da Frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Basta definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para ligar uma política de segurança a uma porta frontal. As atualizações de política subsequentes são automaticamente aplicadas na extremidade frontal.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente](../../frontdoor/front-door-overview.md) 
- Saiba mais sobre [a WAF com Porta da Frente](afds-overview.md)