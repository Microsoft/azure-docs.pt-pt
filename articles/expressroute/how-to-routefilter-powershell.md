---
title: 'Configurar filtros de rota para o emparelhamento da Microsoft-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para o emparelhamento da Microsoft usando o PowerShell
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 7a830b01bb66f807972b642ad46d54d124d16d8d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748135"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configurar filtros de rota para o emparelhamento da Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. As etapas neste artigo ajudam você a configurar e gerenciar filtros de rota para circuitos do ExpressRoute.

Os serviços do Office 365, como o Exchange Online, o SharePoint Online e o Skype for Business, e os serviços públicos do Azure, como o armazenamento e o banco de BD SQL, podem ser acessados por meio do emparelhamento da Microsoft. Os serviços públicos do Azure são selecionáveis em uma base por região e não podem ser definidos por serviço público.

Quando o emparelhamento da Microsoft é configurado em um circuito do ExpressRoute e um filtro de rota é anexado, todos os prefixos selecionados para esses serviços são anunciados por meio das sessões BGP estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores de comunidade BGP e os serviços que eles mapeiam, consulte [comunidades BGP](expressroute-routing.md#bgp).

Se você precisar de conectividade com todos os serviços, um grande número de prefixos será anunciado por meio do BGP. Isso aumenta significativamente o tamanho das tabelas de rotas mantidas pelos roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos por meio do emparelhamento da Microsoft, é possível reduzir o tamanho das tabelas de rotas de duas maneiras. Pode:

- Filtre prefixos indesejados aplicando filtros de rota em comunidades BGP. Essa é uma prática de rede padrão e é usada normalmente em muitas redes.

- Defina os filtros de rota e aplique-os ao circuito do ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Os roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre filtros de rota

Quando o emparelhamento da Microsoft é configurado no circuito do ExpressRoute, os roteadores de borda de rede da Microsoft estabelecem um par de sessões BGP com os roteadores de borda (seu ou seu provedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de permissões de todos os valores de comunidade BGP. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder anexar filtros de rota aos serviços do Office 365, você deve ter autorização para consumir os serviços do Office 365 por meio do ExpressRoute. Se você não estiver autorizado a consumir os serviços do Office 365 por meio do ExpressRoute, a operação para anexar filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos de ExpressRoute que foram configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados por meio do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos do ExpressRoute que são configurados em ou após 1º de agosto de 2017 não terão prefixos anunciados até que um filtro de rota seja anexado ao circuito.
> 
> 

### <a name="workflow"></a>Modelo

Para poder se conectar com êxito aos serviços por meio do emparelhamento da Microsoft, você deve concluir as seguintes etapas de configuração:

- Você deve ter um circuito do ExpressRoute ativo que tenha o emparelhamento da Microsoft provisionado. Você pode usar as instruções a seguir para realizar essas tarefas:
  - [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e tenha o circuito habilitado pelo seu provedor de conectividade antes de continuar. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado.
  - [Crie um emparelhamento da Microsoft](expressroute-circuit-peerings.md) se você gerenciar a sessão BGP diretamente. Ou então, peça ao seu provedor de conectividade para provisionar o emparelhamento da Microsoft para seu circuito.

-  Você deve criar e configurar um filtro de rota.
    - Identificar os serviços que você deseja consumir por meio do emparelhamento da Microsoft
    - Identificar a lista de valores de comunidade BGP associados aos serviços
    - Criar uma regra para permitir que a lista de prefixos corresponda aos valores de comunidade BGP

-  Você deve anexar o filtro de rota ao circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você atende aos seguintes critérios:

 - Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado.

 - Você deve ter um emparelhamento da Microsoft ativo. Siga as instruções no artigo [criar e modificar a configuração de emparelhamento](expressroute-circuit-peerings.md) .


### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Iniciar sessão na sua conta do Azure

Antes de iniciar esta configuração, deve iniciar sessão na conta do Azure. O cmdlet pede-lhe as credenciais de início de sessão da conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

Abra a consola do PowerShell com privilégios elevados e ligue-se à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar. Se você estiver usando Azure Cloud Shell, não precisará executar esse cmdlet, pois você será conectado automaticamente.

```azurepowershell
Connect-AzAccount
```

Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a subscrição que pretende utilizar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Etapa 1: obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. obter uma lista de valores de comunidade BGP

Use o cmdlet a seguir para obter a lista de valores de comunidade BGP associados aos serviços acessíveis por meio do emparelhamento da Microsoft e a lista de prefixos associados a eles:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. faça uma lista dos valores que você deseja usar

Faça uma lista de valores de comunidade BGP que você deseja usar no filtro de rota.

## <a name="filter"></a>Etapa 2: criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e a regra deve ser do tipo ' Allow '. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. criar um filtro de rota

Primeiro, crie o filtro de rota. O comando ' New-AzRouteFilter ' cria apenas um recurso de filtro de rota. Depois de criar o recurso, você deve criar uma regra e anexá-la ao objeto de filtro de rota. Execute o seguinte comando para criar um recurso de filtro de rota:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. criar uma regra de filtro

Você pode especificar um conjunto de comunidades BGP como uma lista separada por vírgulas, conforme mostrado no exemplo. Execute o seguinte comando para criar uma nova regra:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. adicionar a regra ao filtro de rota

Execute o seguinte comando para adicionar a regra de filtro ao filtro de rota:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Etapa 3: anexar o filtro de rota a um circuito de ExpressRoute

Execute o comando a seguir para anexar o filtro de rota ao circuito do ExpressRoute, supondo que você tenha apenas emparelhamento da Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, use as seguintes etapas:

1. Execute o seguinte comando para obter o recurso de filtro de rota:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Obtenha as regras de filtro de rota para o recurso de filtro de rota executando o seguinte comando:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já estiver anexado a um circuito, as atualizações para a lista de comunidade BGP propagarão automaticamente as alterações de anúncio de prefixo apropriadas por meio das sessões BGP estabelecidas. Você pode atualizar a lista de comunidades BGP do seu filtro de rota usando o seguinte comando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Para desanexar um filtro de rota de um circuito do ExpressRoute

Depois que um filtro de rota é desanexado do circuito do ExpressRoute, nenhum prefixo é anunciado por meio da sessão BGP. Você pode desanexar um filtro de rota de um circuito do ExpressRoute usando o seguinte comando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Para excluir um filtro de rota

Você só poderá excluir um filtro de rota se ele não estiver anexado a nenhum circuito. Verifique se o filtro de rota não está anexado a nenhum circuito antes de tentar excluí-lo. Você pode excluir um filtro de rota usando o seguinte comando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
