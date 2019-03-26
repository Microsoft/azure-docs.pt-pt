---
title: Tutorial - configurar a filtragem geográfica web de política de firewall de aplicações para o serviço de porta de entrada do Azure
description: Neste tutorial, vai aprender a criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: 371347149b3c3f14784ba62365cfd6224ded99d1
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407339"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Como configurar uma política de WAF filtragem geográfica para a porta de entrada
Este tutorial mostra como utilizar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente. Esta política de filtragem geográfica de exemplo irá bloquear os pedidos de todos os outros países, exceto dos Estados Unidos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de filtragem geográfica, como configurar o ambiente do PowerShell e criar um perfil de porta de entrada.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as credenciais do Azure e instale o módulo do PowerShell de Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligar ao Azure com uma caixa de diálogo de início de sessão interativa
```
Connect-AzAccount
Install-Module -Name Az
```
Certifique-se de que tem a versão atual do PowerShellGet instalado. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

### <a name="create-a-front-door-profile"></a>Criar um perfil de porta de entrada
Criar um perfil de porta de entrada ao seguir as instruções descritas em [início rápido: Criar um perfil de porta de entrada](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definir a filtragem geográfica corresponde à condição

Criar uma condição de correspondência de exemplo que seleciona os pedidos não provenientes de "US" usando [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) nos parâmetros durante a criação de uma condição de correspondência. São fornecidos dois códigos de país de letra para o mapeamento de país [aqui](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Adicionar condições de correspondência da filtragem geográfica a uma regra com Ação e Prioridade

Criar um objeto de CustomRule `nonUSBlockRule` com base na condição de correspondência, uma ação e usando uma prioridade [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorcustomruleobject).  Um objeto CustomRule pode ter vários MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Adicionar regras a uma política
Encontrar o nome do grupo de recursos que contém o perfil de porta de entrada usando `Get-AzResourceGroup`. Em seguida, crie uma `geoPolicy` objeto de política que contém `nonUSBlockRule` usando [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) no grupo de recursos especificado que contém o perfil de porta de entrada. Tem de fornecer um nome exclusivo para a política de georreplicação. 

O exemplo abaixo utiliza o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que criou desde o início de perfil de acordo com as instruções fornecidas no [início rápido: Criar uma porta de entrada](quickstart-create-front-door.md) artigo.

```
$geoPolicy = New-AzFrontDoorFireWallPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Política de ligação WAF para um anfitrião de front-end de porta de entrada
Ligar o objeto de política de WAF para o anfitrião de front-end de porta de entrada existente e atualizar as propriedades de porta de entrada. 

Para tal, primeiro recuperar o objeto de porta de entrada através de [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Em seguida, defina a propriedade de WebApplicationFirewallPolicyLink de front-end como o resourceId da `geoPolicy`usando [conjunto AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Apenas terá de definir a propriedade de WebApplicationFirewallPolicyLink uma vez para ligar uma política de WAF para um anfitrião de front-end de porta de entrada. Atualizações à política subsequentes são aplicadas automaticamente para o anfitrião de front-end.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a [segurança de camada de aplicação com o Front Door](front-door-application-security.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
