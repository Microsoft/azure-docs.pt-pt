---
title: Tutorial - Política de filtragem geo-filtrante Configure WAF - Porta frontal Azure
description: Neste tutorial, aprende-se a criar uma política de WAF de geo-filtragem e associa a política ao anfitrião frontend existente.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324031"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Tutorial: Como configurar uma política de WAF de geo-filtragem para a sua Porta frontal
Este tutorial mostra como utilizar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente. Esta política de geo-filtragem da amostra bloqueará os pedidos de todos os outros países/regiões, com exceção dos Estados Unidos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Defina a condição de correspondência de geo filtragem.
> - Adicione a condição de correspondência de geo filtragem a uma regra.
> - Adicione regras a uma política.
> - Ligue a política da WAF ao anfitrião frontdoor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos
* Antes de começar a configurar uma política de geo-filtro, configurar o seu ambiente PowerShell e criar um perfil da Porta Frontal.
* Crie uma porta frontal seguindo as instruções descritas no [Quickstart: Crie um perfil da porta da frente](quickstart-create-front-door.md).

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
Encontre o nome do grupo de recursos que contém o perfil da porta frontal utilizando `Get-AzResourceGroup` . Em seguida, crie um `geoPolicy` objeto de política contendo a `nonUSBlockRule`  utilização da [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil da porta frontal. Deve fornecer um nome único para a política de geo-filtragem. 

O exemplo abaixo utiliza o nome do Grupo de Recursos *FrontDoorQS_rg0* com o pressuposto de que criou o perfil da porta frontal usando instruções fornecidas no [Quickstart: Criar um](quickstart-create-front-door.md) artigo da Porta frontal. No exemplo abaixo, substitua o nome político *geoPolicyAllowUSOnly* por um nome político único.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Ligue a política da WAF a um anfitrião frontend da porta da frente
Ligue o objeto de política WAF ao anfitrião frontend da porta frontal existente e atualize as propriedades da Porta Frontal. 

Para tal, recupere primeiro o seu objeto da porta frontal utilizando [o Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Em seguida, deite a propriedade frontend WebApplicationFirewallPolicyLink para os recursosId do `geoPolicy` [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Só precisa de definir a propriedade WebApplicationFirewallPolicyLink uma vez para ligar uma política da WAF a um anfitrião frontend front door. As atualizações de política subsequentes são aplicadas automaticamente ao anfitrião frontal.

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, configuraste uma regra de geo-filtragem que está associada a uma política da WAF. Em seguida, ligou a apólice a um anfitrião frontal da sua porta da frente. Se já não necessitar da regra de geofillagem ou da política da WAF, deve primeiro dissociar a política do anfitrião frontal antes de a política da WAF poder ser eliminada.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Política desassociada da WAF":::

## <a name="next-steps"></a>Passos seguintes

Para aprender a configurar uma Firewall de Aplicação Web para a sua Porta frontal, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Firewall de aplicações Web e Front Door](front-door-waf.md)
