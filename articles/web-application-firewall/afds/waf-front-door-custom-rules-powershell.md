---
title: Configurar regras personalizadas do WAF & conjunto de regras padrão para a porta frontal do Azure
description: Saiba como configurar uma política de WAF consistir em regras personalizadas e gerenciadas para um ponto de extremidade de porta de recepção existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186639"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurar uma política de firewall do aplicativo Web usando Azure PowerShell

A política do WAF (firewall do aplicativo Web) do Azure define as inspeções necessárias quando uma solicitação chega na porta da frente.
Este artigo mostra como configurar uma política de WAF que consiste em algumas regras personalizadas e com o conjunto de regras padrão gerenciadas pelo Azure habilitado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar uma política de limite de taxa, configure o ambiente do PowerShell e crie um perfil de porta frontal.

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instalar o módulo AZ PowerShell.

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```
Connect-AzAccount

```
Antes de instalar o módulo do Front Door, certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo AZ. FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de porta de front-end

Crie um perfil de porta frontal seguindo as instruções descritas em [início rápido: criar um perfil de porta frontal](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada com base em parâmetros http

O exemplo a seguir mostra como configurar uma regra personalizada com duas condições de correspondência usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). As solicitações são de um site especificado, conforme definido pelo referenciador, e a cadeia de caracteres de consulta não contém "senha". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada com base no método de solicitação http

Crie uma regra bloqueando o método "PUT" usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) da seguinte maneira:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Criar uma regra personalizada com base na restrição de tamanho

O exemplo a seguir cria uma regra de bloqueio de solicitações com URL com mais de 100 caracteres usando Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar conjunto de regras padrão gerenciadas

O exemplo a seguir cria um conjunto de regras padrão gerenciado usando Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Localize o nome do grupo de recursos que contém o perfil de porta frontal usando `Get-AzResourceGroup`. Em seguida, configure uma política de segurança com regras criadas nas etapas anteriores usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de porta frontal.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de link para um host front-end da porta frontal

Vincule o objeto de política de segurança a um host front-end de porta frontal existente e atualize as propriedades da porta frontal. Primeiro, recupere o objeto de porta frontal usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, defina a propriedade *WebApplicationFirewallPolicyLink* de front-end como o *ResourceId* do "$myWAFPolicy $" criado na etapa anterior usando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo usa o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que você criou o perfil de porta frontal usando as instruções fornecidas no artigo [início rápido: criar uma porta frontal](../../frontdoor/quickstart-create-front-door.md) . Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do perfil de porta frontal. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para vincular uma política de segurança a um front-end de porta frontal. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a [porta frontal](../../frontdoor/front-door-overview.md) 
- Saiba mais sobre o [WAF com a porta frontal](afds-overview.md)
