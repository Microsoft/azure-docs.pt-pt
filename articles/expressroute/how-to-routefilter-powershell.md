---
title: 'Tutorial: Configurar filtros de rota para o espreocupar a Microsoft - Azure PowerShell'
description: Este tutorial descreve como configurar filtros de rota para o Microsoft Peering usando o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 90d4def5a1c08e305b9315f299e83e2187b6be2c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969947"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Tutorial: Configurar filtros de rota para a Microsoft a espreitar usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos deste artigo ajudam-no a configurar e a gerir filtros de rota para circuitos ExpressRoute.

Os serviços microsoft 365, tais como Exchange Online, SharePoint Online e Skype for Business, e serviços públicos Azure, como armazenamento e DB SQL são acessíveis através do espreitamento da Microsoft. Os serviços públicos do Azure são selecionáveis por região e não podem ser definidos por serviço público.

Quando o espreitamento da Microsoft é configurado num circuito ExpressRoute, todos os prefixos relacionados com estes serviços são publicitados através das sessões de BGP que são estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades BGP.](expressroute-routing.md#bgp)

A conectividade com todos os serviços Azure e Microsoft 365 causa um grande número de prefixos é anunciado através do BGP. O grande número de prefixos aumenta significativamente o tamanho das tabelas de rota mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do espreitamento da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas maneiras. Pode:

* Filtrar prefixos indesejados aplicando filtros de rota nas comunidades de BGP. A filtragem de rotas é uma prática padrão de networking e é usada comumente dentro de muitas redes.

* Defina os filtros de rota e aplique-os no seu circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do espreitamento da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> - Obtenha valores comunitários BGP.
> - Crie a regra do filtro de rota e do filtro.
> - Filtro de rota associado a um circuito ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o espreguiçadamento da Microsoft é configurado no seu circuito ExpressRoute, os routers do Microsoft Edge estabelecem um par de sessões de BGP com os seus routers Edge através do seu fornecedor de conectividade. Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista permitida de todos os valores comunitários da BGP. Uma vez que um recurso de filtro de rota é definido e ligado a um circuito ExpressRoute, todos os prefixos que mapeam para os valores comunitários BGP são anunciados para a sua rede.

Para anexar filtros de rota com os serviços microsoft 365, tem de ter autorização para consumir os serviços microsoft 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços microsoft 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para obter mais informações sobre o processo de autorização, consulte [a Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 
## <a name="prerequisites"></a>Pré-requisitos

- Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

- Deve ter um circuito ExpressRoute ativo que tenha a Microsoft a espreitar. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Crie um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.
  - [Crie o olho da Microsoft](expressroute-circuit-peerings.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a provisão da Microsoft para o seu circuito.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Obtenha uma lista de prefixos e valores comunitários de BGP

1. Utilize o seguinte cmdlet para obter a lista de valores e prefixos da comunidade BGP associados a serviços acessíveis através do espreguite da Microsoft:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Faça uma lista dos valores comunitários BGP que pretende utilizar no filtro de rota.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Crie um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários BGP associados a ela. O comando `az network route-filter create` apenas cria um recurso de filtro de rota. Depois de criar o recurso, deve então criar uma regra e fixá-la ao objeto do filtro de rota.

1. Para criar um recurso de filtro de rota, execute o seguinte comando:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Para criar uma regra de filtro de rota, executar o seguinte comando:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Executar o seguinte comando para adicionar a regra do filtro ao filtro de rota:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Fixe o filtro de rota a um circuito ExpressRoute

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

Se o filtro de rota já estiver ligado a um circuito, as atualizações da lista comunitária BGP propagam automaticamente as alterações de anúncios de prefixo através da sessão de BGP estabelecida. Pode atualizar a lista comunitária BGP do seu filtro de rota utilizando o seguinte comando:

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

## <a name="clean-up-resources"></a>Limpar recursos

Só pode apagar um filtro de rota se não estiver ligado a nenhum circuito. Certifique-se de que o filtro de rota não está ligado a nenhum circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota utilizando o seguinte comando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre amostras de configuração do router, consulte:

> [!div class="nextstepaction"]
> [Amostras de configuração do router para configurar e gerir o encaminhamento](expressroute-config-samples-routing.md)
