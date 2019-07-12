---
title: Configurar uma política de firewall (WAF) de aplicações web com regras personalizadas e Ruse conjunto predefinidos para a porta da frente - Azure PowerShell
description: Saiba como configurar uma WAF política consistem em regras personalizadas e gerenciadas para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: ff8330ab8aec7f0e9aa92409ce1eafd5be5ceeaf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605761"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configurar uma política de firewall de aplicações web com o Azure PowerShell
Política de firewall (WAF) de aplicações web do Azure define inspeções é necessárias quando um pedido chega à porta de entrada.
Este artigo mostra como configurar uma política de WAF que consiste em algumas regras personalizadas e com geridos pelo Azure Ruse conjunto predefinidos ativada.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, como configurar o ambiente do PowerShell e criar um perfil de porta de entrada.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as credenciais do Azure e instale o módulo do PowerShell de Az.

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
```
Connect-AzAccount

```
Antes de instalar o módulo do Front Door, certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo de Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de porta de entrada
Criar um perfil de porta de entrada ao seguir as instruções descritas em [início rápido: Criar um perfil de porta de entrada](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada com base nos parâmetros de http

O exemplo seguinte mostra como configurar uma regra personalizada com duas condições de correspondência usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Pedidos são de um site especificado, conforme definido pela Referenciador e cadeia de consulta não contém "palavra-passe". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada com base no método de pedido de http
Criar uma regra de bloqueio "Colocar" usando o método [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) da seguinte forma:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Criar uma regra personalizada com base na restrição de tamanho

O exemplo seguinte cria uma regra a bloquear pedidos com o Url que é mais de 100 carateres, com o Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar gerido predefinido de conjunto de regras

O exemplo seguinte cria um gerido regra conjunto predefinidos com o Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontrar o nome do grupo de recursos que contém o perfil de porta de entrada usando `Get-AzResourceGroup`. Em seguida, configure uma política de segurança com regras criadas nos passos anteriores mediante [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de porta de entrada.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de ligação para um anfitrião de front-end de porta de entrada
Ligar o objeto de política de segurança para um anfitrião de front-end de porta de entrada existente e atualizar as propriedades de porta de entrada. Primeiro, recuperar o objeto de porta de entrada usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, configure o front-end *WebApplicationFirewallPolicyLink* propriedade para o *resourceId* do "$$myWAFPolicy" criado no passo anterior com [conjunto-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo utiliza o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que criou desde o início de perfil de acordo com as instruções fornecidas no [início rápido: Criar uma porta de entrada](quickstart-create-front-door.md) artigo. Além disso, no exemplo abaixo, substitua $frontDoorName com o nome do seu perfil de porta de entrada. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para ligar uma política de segurança a uma porta de entrada front-end. Atualizações à política subsequentes são automaticamente aplicadas no front-end.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [porta de entrada](front-door-overview.md) 
- Saiba mais sobre [WAF para a porta de entrada](waf-overview.md)
