---
title: Tutorial - Política de filtragem geo-filtrante Configure WAF - Porta frontal Azure
description: Neste tutorial, aprende-se a criar uma política de geo-filtragem e associa a política ao seu anfitrião frontend da Porta da Frente
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: 2ce55ee50a2b302712973b99dc6503f1e6bbc425
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074792"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Como configurar uma política WAF de filtragem geográfica para o Front Door
Este tutorial mostra como utilizar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente. Esta política de geo-filtragem da amostra bloqueará os pedidos de todos os outros países/regiões, com exceção dos Estados Unidos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de geo-filtro, configurar o seu ambiente PowerShell e criar um perfil da Porta Frontal.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sômposições com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue ao Azure com um diálogo interativo para o sent-in
```
Install-Module -Name Az
Connect-AzAccount
```
Certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalar módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Criar um perfil da porta da frente
Crie um perfil da porta frontal seguindo as instruções descritas no [Quickstart: Crie um perfil da porta da frente](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definir condição de jogo de geo-filtragem

Crie uma condição de correspondência de amostra que selecione pedidos que não vêm de "EUA" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) em parâmetros ao criar uma condição de correspondência. São fornecidos [códigos](front-door-geo-filtering.md)de dois países/regiões para o mapeamento país/região.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Adicionar condições de correspondência da filtragem geográfica a uma regra com Ação e Prioridade

Crie um objeto CustomRule `nonUSBlockRule` baseado na condição de correspondência, uma ação e uma prioridade usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Um objeto CustomRule pode ter vários MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Adicione regras a uma política
Encontre o nome do grupo de recursos que contém o perfil da porta frontal utilizando `Get-AzResourceGroup` . Em seguida, crie um `geoPolicy` objeto de política contendo a `nonUSBlockRule` utilização da [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil da porta frontal. Deve fornecer um nome único para a política de geo-filtragem. 

O exemplo abaixo utiliza o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil da Porta Frontal usando instruções fornecidas no [Quickstart: Criar um](quickstart-create-front-door.md) artigo da Porta Frontal. No exemplo abaixo, substitua o nome político *geoPolicyAllowUSOnly* por um nome político único.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Ligue a política da WAF a um anfitrião frontend da porta da frente
Ligue o objeto de política WAF ao anfitrião frontend da porta frontal existente e atualize as propriedades da Porta Frontal. 

Para tal, recupere primeiro o seu objeto da porta frontal utilizando [o Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Em seguida, deite a propriedade frontend WebApplicationFirewallPolicyLink para os recursosId do `geoPolicy` [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Só precisa de definir a propriedade WebApplicationFirewallPolicyLink uma vez para ligar uma política da WAF a um anfitrião frontend front door. As atualizações de política subsequentes são aplicadas automaticamente ao anfitrião frontal.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a firewall da aplicação web Azure](waf-overview.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
