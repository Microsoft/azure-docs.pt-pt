---
title: Configure regra limite de taxa WAF para porta frontal - Azure PowerShell
description: Aprenda a configurar uma regra de limite de tarifa para um ponto final existente da Porta da Frente.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649369"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configure uma regra de limite de taxa de firewall de aplicação web usando O PowerShell Azure
A regra limite de limite de tarifa Azure Web Application Firewall (WAF) para a Porta Frontal Azure controla o número de pedidos permitidos aos clientes durante um minuto de duração.
Este artigo mostra como configurar uma regra limite de taxa WAF que controla o número de pedidos permitidos dos clientes para uma aplicação web que contém */promo* no URL usando O Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxas, crie o seu ambiente PowerShell e crie um perfil front door.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue-se a Azure com um diálogo interativo para iniciar sessão
```
Connect-AzAccount

```
Antes de instalar o módulo Porta Frontal, certifique-se de que a versão atual do PowerShellGet está instalada. Executar o seguinte comando e reabrir powerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instale módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil porta da frente
Crie um perfil porta da frente seguindo as instruções descritas em [Quickstart: Criar um perfil porta da frente](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definir condições de correspondência de url
Defina uma condição de correspondência de URL (URL contém /promo) utilizando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
O exemplo seguinte corresponde */promo* como o valor da variável *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Criar uma regra de limite de tarifa personalizada
Detete um limite de tarifa usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). No exemplo seguinte, o limite é fixado para 1000. Os pedidos de qualquer cliente para a página promocional superior a 1000 durante um minuto estão bloqueados até ao início do minuto seguinte.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil porta da frente usando `Get-AzureRmResourceGroup`. Em seguida, configure uma política de segurança com uma regra de limite de tarifa personalizada usando [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil porta da frente.

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil porta da frente usando instruções fornecidas no [Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md)

 usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Link política para um anfitrião frontal da porta da frente
Ligue o objeto de política de segurança a um anfitrião frontal existente e atualize as propriedades da Porta da Frente. Primeiro, recupere o objeto da porta da frente utilizando o comando [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Em seguida, detete teda a propriedade *frontal WebApplicationFirewallPolicyLink* para o *recurso Id* do "$ratePolicy" criado no passo anterior utilizando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil porta da frente usando instruções fornecidas no [Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md) Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil porta da frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Só precisa de definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para ligar uma política de segurança a uma frente frontal da Porta Da Frente. As atualizações de políticas subsequentes são automaticamente aplicadas na parte frontal.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente.](../../frontdoor/front-door-overview.md) 


