---
title: Configurar uma regra de limite de taxa de firewall de aplicação de web para a porta da frente - Azure PowerShell
description: Saiba como configurar uma regra de limite de taxa para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 903405c8fada6165b79e780a7828c6de3b95163e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478916"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurar uma web application firewall taxa limite regra com o Azure PowerShell
A regra de limite de taxa na firewall (WAF) de aplicação web do Azure para a porta da frente do Azure controla o número de pedidos permitidos de um IP de cliente único durante um período de um minuto.
Este artigo mostra como configurar uma regra de limite de taxa de WAF que controla o número de pedidos de permissão de um único cliente para uma aplicação web que contém */promo* no URL com o Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, como configurar o ambiente do PowerShell e criar um perfil de porta de entrada.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as credenciais do Azure e instale o módulo do PowerShell de Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligar ao Azure com uma caixa de diálogo de início de sessão interativa
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

## <a name="define-url-match-conditions"></a>Definir condições de correspondência de url
Definir uma condição de correspondência de URL (URL contém /promo) usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
O exemplo a seguir corresponde */promo* como o valor da *RequestUri* variável:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Criar uma regra de limite de taxa personalizado
Definir um limite de taxa utilizando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). No exemplo a seguir, o limite é definido como 1000. Pedidos de qualquer cliente para a página de promoção exceder 1000 durante um minuto estão bloqueados até o minuto seguinte é iniciado.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontrar o nome do grupo de recursos que contém o perfil de porta de entrada usando `Get-AzureRmResourceGroup`. Em seguida, configure uma política de segurança com um limite de taxa personalizada regra usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de porta de entrada.

O exemplo abaixo utiliza o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que criou desde o início de perfil de acordo com as instruções fornecidas no [início rápido: Criar uma porta de entrada](quickstart-create-front-door.md) artigo.

 usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de ligação para um anfitrião de front-end de porta de entrada
Ligar o objeto de política de segurança para um anfitrião de front-end de porta de entrada existente e atualizar as propriedades de porta de entrada. Primeiro, recuperar o objeto de porta de entrada usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) comando.
Em seguida, configure o front-end *WebApplicationFirewallPolicyLink* propriedade para o *resourceId* de "$ratePolicy" criado no passo anterior com [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comando. 

O exemplo abaixo utiliza o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que criou desde o início de perfil de acordo com as instruções fornecidas no [início rápido: Criar uma porta de entrada](quickstart-create-front-door.md) artigo. Além disso, no exemplo abaixo, substitua $frontDoorName com o nome do seu perfil de porta de entrada. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para ligar uma política de segurança a uma porta de entrada front-end. Atualizações à política subsequentes são automaticamente aplicadas no front-end.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [porta de entrada](front-door-overview.md) 


