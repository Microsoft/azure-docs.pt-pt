---
title: Tutorial – configurar a política de firewall do aplicativo Web de filtragem geográfica para o serviço de porta frontal do Azure
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
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e40e99aa57d10bd69143efc8db38ac0071d8952f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827765"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Como configurar uma política de WAF de filtragem geográfica para sua porta frontal
Este tutorial mostra como utilizar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente. Esta política de filtragem geográfica de exemplo bloqueará solicitações de todos os outros países/regiões, exceto Estados Unidos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de filtro geográfico, configure o ambiente do PowerShell e crie um perfil de porta frontal.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instalar o módulo AZ PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conectar-se ao Azure com uma caixa de diálogo interativa para entrar
```
Install-Module -Name Az
Connect-AzAccount
```
Verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalar o módulo AZ. FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Criar um perfil de porta de front-end
Crie um perfil de porta frontal seguindo as instruções descritas [em início rápido: Crie um perfil](quickstart-create-front-door.md)de porta frontal.

## <a name="define-geo-filtering-match-condition"></a>Definir condição de correspondência de filtragem geográfica

Crie uma condição de correspondência de exemplo que selecione solicitações não provenientes de "US" usando os parâmetros [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) ao criar uma condição de correspondência. Os códigos de país de duas letras para o mapeamento de país são fornecidos [aqui](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Adicionar condições de correspondência da filtragem geográfica a uma regra com Ação e Prioridade

Crie um objeto `nonUSBlockRule` CustomRule com base na condição de correspondência, uma ação e uma prioridade usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Um objeto CustomRule pode ter vários MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Adicionar regras a uma política
Localize o nome do grupo de recursos que contém o perfil de porta frontal `Get-AzResourceGroup`usando. Em seguida, crie `geoPolicy` um objeto de `nonUSBlockRule` política com o uso de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil de porta frontal. Você deve fornecer um nome exclusivo para a política geográfica. 

O exemplo abaixo usa o nome do grupo de recursos *myResourceGroupFD1* com a suposição de que você criou o perfil de porta frontal usando [as instruções fornecidas no guia de início rápido: Crie um artigo de](quickstart-create-front-door.md) porta frontal. No exemplo abaixo, substitua o nome da política *geoPolicyAllowUSOnly* por um nome de política exclusivo.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Vincular a política WAF a um host front-end de porta frontal
Vincule o objeto de política WAF ao host de front-end da porta frontal existente e atualize as propriedades da porta frontal. 

Para fazer isso, primeiro recupere o objeto de porta frontal usando [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Em seguida, defina a propriedade front-end WebApplicationFirewallPolicyLink para o `geoPolicy`ResourceId do usando [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Você só precisa definir a propriedade WebApplicationFirewallPolicyLink uma vez para vincular uma política de WAF a um host de front-end de porta frontal. As atualizações de política subsequentes são aplicadas automaticamente ao host de front-end.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [Firewall do aplicativo Web do Azure](waf-overview.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
