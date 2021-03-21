---
title: Regra limite de taxa WAF configurada para porta frontal - Azure PowerShell
description: Aprenda a configurar uma regra limite de taxa para um ponto final da porta frontal existente.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 23b893bad591af5f1e923b68e8d30453f859792b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563482"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configure uma regra limite de taxa de firewall de aplicação web usando Azure PowerShell
A regra limite de taxa Azure Web Application Firewall (WAF) para Azure Front Door controla o número de pedidos permitidos aos clientes durante um minuto de duração.
Este artigo mostra como configurar uma regra de limite de taxa WAF que controla o número de pedidos permitidos dos clientes para uma aplicação web que contém */promo* no URL usando Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> São aplicados limites de tarifas para cada endereço IP do cliente. Se tiver vários clientes a aceder à sua Porta frontal a partir de diferentes endereços IP, eles terão os seus próprios limites de tarifa aplicados.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, crie o seu ambiente PowerShell e crie um perfil da Porta Frontal.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](/powershell/azure/) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sômposições com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue ao Azure com um diálogo interativo para iniciar sação
```
Connect-AzAccount

```
Antes de instalar o módulo Porta Frontal, certifique-se de que a versão atual do PowerShellGet está instalada. Executar o seguinte comando e reabrir PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil da porta da frente
Crie um perfil da porta frontal seguindo as instruções descritas no [Quickstart: Criar um perfil da porta frontal](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definir condições de correspondência de URL
Defina uma condição de correspondência de URL (URL contém /promo) utilizando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
O exemplo a seguir corresponde */promo* como o valor da variável *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Criar uma regra de limite de taxa personalizada
Estabeleça um limite de tarifa usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). No exemplo seguinte, o limite é fixado para 1000. Os pedidos de qualquer cliente para a página promocional superior a 1000 durante um minuto estão bloqueados até ao início do minuto seguinte.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configure uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil da porta frontal utilizando `Get-AzureRmResourceGroup` . Em seguida, configurar uma política de segurança com uma regra de limite de taxa personalizada usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil da porta da frente.

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil da Porta Frontal usando instruções fornecidas no [Quickstart: Criar um](../../frontdoor/quickstart-create-front-door.md) artigo front door, utilizando [a New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de ligação a um anfitrião frontal da porta da frente
Ligue o objeto da política de segurança a um anfitrião frontal existente e atualize as propriedades da Porta Frontal. Primeiro, recupere o objeto da porta frontal utilizando o comando [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Em seguida, desenrida a propriedade *frontal WebApplicationFirewallPolicyLink* para os *recursosId* do "$ratePolicy" criado no passo anterior usando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil da Porta Frontal usando instruções fornecidas no [Quickstart: Criar um](../../frontdoor/quickstart-create-front-door.md) artigo da Porta Frontal. Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil da Porta da Frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Basta definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para ligar uma política de segurança a uma porta frontal. As atualizações de política subsequentes são automaticamente aplicadas na extremidade frontal.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente.](../../frontdoor/front-door-overview.md)