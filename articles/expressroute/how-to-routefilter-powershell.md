---
title: 'ExpressRoute: Filtros de rota- Microsoft peering:Azure PowerShell'
description: Este artigo descreve como configurar filtros de rota para o Microsoft Peering usando o PowerShell
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c4ca4362f10ea6ed2fa7cc39370fc9b4c764ff3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566199"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configure filtros de rota para o espreomador da Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos deste artigo ajudam-no a configurar e a gerir filtros de rota para circuitos ExpressRoute.

Os serviços microsoft 365, tais como Exchange Online, SharePoint Online e Skype for Business, e serviços públicos Azure, como armazenamento e DB SQL são acessíveis através do espreitamento da Microsoft. Os serviços públicos do Azure são selecionáveis por região e não podem ser definidos por serviço público.

Quando o espreitamento da Microsoft é configurado num circuito ExpressRoute e um filtro de rota é anexado, todos os prefixos que são selecionados para estes serviços são anunciados através das sessões BGP que são estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades BGP.](expressroute-routing.md#bgp)

Se necessitar de conectividade a todos os serviços, um grande número de prefixos são anunciados através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do espreitamento da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas maneiras. Pode:

- Filtrar prefixos indesejados aplicando filtros de rota nas comunidades de BGP. Esta é uma prática padrão de networking e é usada comumente em muitas redes.

- Defina os filtros de rota e aplique-os no seu circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do espreitamento da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o espreguiçadeira da Microsoft está configurado no seu circuito ExpressRoute, os routers de borda de rede da Microsoft estabelecem um par de sessões de BGP com os routers de borda (o seu ou o do seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de todos os valores comunitários da BGP. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder anexar filtros de rota com os serviços Microsoft 365, tem de ter autorização para consumir os serviços da Microsoft 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços microsoft 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para obter mais informações sobre o processo de autorização, consulte [a Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para conseguir ligar-se com sucesso aos serviços através do estomamento da Microsoft, tem de completar os seguintes passos de configuração:

- Deve ter um circuito ExpressRoute ativo que tenha a Microsoft a espreitar. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Crie um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de prosseguir. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.
  - [Crie o olho da Microsoft](expressroute-circuit-peerings.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a provisão da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identifique os serviços com que consome através do espremo da Microsoft
    - Identificar a lista de valores comunitários BGP associados aos serviços
    - Crie uma regra que permita que a lista de prefixos corresponda aos valores da comunidade BGP

-  Deve fixar o filtro de rota ao circuito ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que cumpre os seguintes critérios:

 - Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.

 - Deve ter um Microsoft ativo a espreitar. Siga as instruções no artigo [de configuração de esprevação e modificação.](expressroute-circuit-peerings.md)


### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Iniciar sessão na sua conta do Azure

Antes de iniciar esta configuração, deve iniciar sessão na conta do Azure. O cmdlet pede-lhe as credenciais de início de sessão da conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

Abra a consola do PowerShell com privilégios elevados e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar a ligar. Se estiver a utilizar o Azure Cloud Shell, não precisa de executar este cmdlet, uma vez que será automaticamente inscrito.

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passo 1: Obtenha uma lista de prefixos e valores comunitários de BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenha uma lista dos valores comunitários do BGP

Utilize o cmdlet seguinte para obter a lista de valores comunitários BGP associados a serviços acessíveis através do estomamento da Microsoft, e a lista de prefixos associados a eles:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que pretende utilizar

Faça uma lista dos valores comunitários BGP que pretende utilizar no filtro de rota.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Primeiro, crie o filtro de rota. O comando 'New-AzRouteFilter' cria apenas um recurso de filtro de rota. Depois de criar o recurso, deve então criar uma regra e fixá-la ao objeto do filtro de rota. Executar o seguinte comando para criar um recurso de filtro de rota:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode especificar um conjunto de comunidades de BGP como uma lista separada por vírgula, como mostra o exemplo. Executar o seguinte comando para criar uma nova regra:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Adicione a regra ao filtro de rota

Executar o seguinte comando para adicionar a regra do filtro ao filtro de rota:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passo 3: Fixe o filtro de rota a um circuito ExpressRoute

Executar o seguinte comando para ligar o filtro de rota ao circuito ExpressRoute, assumindo que só tem a Microsoft a espreitar:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize os seguintes passos:

1. Executar o seguinte comando para obter o recurso de filtro de rota:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Obtenha as regras do filtro de rota para o recurso de filtro de rota, executando o seguinte comando:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já estiver ligado a um circuito, as atualizações à lista comunitária BGP propagam automaticamente as alterações de anúncios de prefixo apropriados através das sessões de BGP estabelecidas. Pode atualizar a lista comunitária BGP do seu filtro de rota utilizando o seguinte comando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para separar um filtro de rota de um circuito ExpressRoute

Uma vez que um filtro de rota é desligado do circuito ExpressRoute, nenhum prefixo é anunciado através da sessão BGP. Pode retirar um filtro de rota de um circuito ExpressRoute utilizando o seguinte comando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para eliminar um filtro de rota

Só pode apagar um filtro de rota se não estiver ligado a nenhum circuito. Certifique-se de que o filtro de rota não está ligado a nenhum circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota utilizando o seguinte comando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)
