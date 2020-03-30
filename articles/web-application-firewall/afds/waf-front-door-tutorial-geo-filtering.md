---
title: Configure a política de firewall de aplicação web de geofiltração para o serviço Azure Front Door
description: Neste tutorial, você aprende a criar uma política de geofiltração e associar a política com o seu anfitrião frontend frontdoor existente
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135875"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Configurar uma política de WAF geofiltrante para a sua Porta da Frente

Este tutorial mostra como utilizar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associá-la ao anfitrião de front-end do Front Door existente. Esta política de geofiltração da amostra bloqueará os pedidos de todos os outros países/regiões, com exceção dos Estados Unidos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar uma política de geofiltros, crie o seu ambiente PowerShell e crie um perfil front door.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerir os recursos do Azure. 

Pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Siga as instruções na página, para iniciar sessão com as suas credenciais Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ligue-se a Azure com um diálogo interativo para iniciar sessão

```
Install-Module -Name Az
Connect-AzAccount
```
Certifique-se de que tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instale módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Criar um perfil porta da frente

Crie um perfil porta da frente seguindo as instruções descritas em [Quickstart: Crie um perfil porta da frente](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Defina a condição de correspondência de geofiltração

Crie uma condição de correspondência de amostra seletiva que selecione pedidos que não vêm de "EUA" usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) em parâmetros ao criar uma condição de jogo. Duas letras códigos de país para mapeamento de país são fornecidos em [O que é geo-filtragem em um domínio para Azure Front Door?](waf-front-door-geo-filtering.md)

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Adicionar condições de correspondência da filtragem geográfica a uma regra com Ação e Prioridade

Crie um `nonUSBlockRule` objeto CustomRule baseado na condição de jogo, uma ação e uma prioridade usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Um objeto CustomRule pode ter vários MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Adicionar regras a uma política

Encontre o nome do grupo de recursos `Get-AzResourceGroup`que contém o perfil porta da frente utilizando . Em seguida, `geoPolicy` crie `nonUSBlockRule` um objeto político contendo a utilização [de New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil porta da frente. Deve fornecer um nome único para a geopolítica. 

O exemplo seguinte usa o nome do Grupo de Recursos *myResourceGroupFD1* com o pressuposto de que criou o perfil porta da frente usando instruções fornecidas no [Quickstart: Criar um](../../frontdoor/quickstart-create-front-door.md) artigo porta da frente. No exemplo abaixo, substitua o nome de política *geoPolicyAllowUSOnly* por um nome de política único.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Link WAF política para um anfitrião frontend porta da frente

Ligue o objeto de política WAF ao anfitrião frontfront frontend existente e atualize as propriedades da Porta da Frente. 

Para tal, primeiro recupere o objeto da porta da frente utilizando [o Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Em seguida, detete a propriedade frontend WebApplicationFirewallPolicyLink para o recursoId do `geoPolicy` [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)utilizando .

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Só precisa de definir a propriedade WebApplicationFirewallPolicyLink uma vez para ligar uma política waf a um anfitrião frontend front door. As atualizações de política subsequentes são automaticamente aplicadas ao anfitrião frontal.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a firewall de aplicação web Azure](../overview.md).
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).
