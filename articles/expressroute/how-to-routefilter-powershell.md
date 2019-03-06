---
title: 'Configurar filtros de rota para peering da Microsoft - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para Peering da Microsoft com o PowerShell
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 680bd80261e1f8b026f6e885156b2ef090b0764d
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404489"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configure filtros de rota para peering da Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam-na configurar e gerir filtros de rota para circuitos do ExpressRoute.

Serviços do Dynamics 365 e serviços do Office 365 como o Exchange Online, SharePoint Online e Skype para empresas e serviços público do Azure, como o armazenamento e BD SQL estão acessíveis através do peering da Microsoft. Serviços público do Azure são selecionáveis numa base por região e não pode ser definidos por serviço público.

Quando o peering da Microsoft está configurado num circuito do ExpressRoute e está ligado um filtro de rota, todos os prefixos estão selecionados para estes serviços são anunciados através as sessões de BGP estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista de valores das Comunidades do BGP e os serviços podem ser mapeados para, consulte [Comunidades do BGP](expressroute-routing.md#bgp).

Se precisar de uma conectividade a todos os serviços, um grande número de prefixos é anunciado através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantido por routers na sua rede. Se planear consumir apenas um subconjunto de serviços oferecidos através do peering da Microsoft, pode reduzir o tamanho de suas tabelas de rota de duas formas. Pode:

- Filtre prefixos indesejados ao aplicar filtros de rota em Comunidades BGP. Esta é uma prática padrão de rede e é usada normalmente muitas redes.

- Definir filtros de rota e aplicá-las para o seu circuito do ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que pretende consumir através do peering da Microsoft. Routers de ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre filtros de rota

Se o peering da Microsoft é configurado no seu circuito do ExpressRoute, os routers de limite de Microsoft estabelecem um par de sessões BGP com os routers de limite (sua ou seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de permissões de todos os valores da comunidade BGP. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder-se de que filtros de rota com serviços do Office 365 nos mesmos de anexar, tem de ter autorização para consumir os serviços do Office 365 através do ExpressRoute. Se não está autorizado a consumir os serviços do Office 365 através do ExpressRoute, a operação para anexar os filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Conectividade dos serviços do Dynamics 365 não requer qualquer autorização anterior.

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito.
> 
> 

### <a name="workflow"></a>Workflow

Para poder ligar com êxito aos serviços através do peering da Microsoft, tem de concluir os seguintes passos de configuração:

- Tem de ter um circuito ExpressRoute ativo que tenha aprovisionado de peering da Microsoft. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e ter o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.
  - [Criar o peering da Microsoft](expressroute-circuit-peerings.md) se gerir diretamente a sessão de BGP. Ou, ter o seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identificar os serviços que com a consumir através do peering da Microsoft
    - Identificar a lista de valores de Comunidade do BGP associado aos serviços
    - Criar uma regra para permitir que a lista de prefixo que corresponda os valores de Comunidade do BGP

-  Terá de anexar o filtro de rota ao circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que cumpre os seguintes critérios:

 - Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.

 - Tem de ter um peering da Microsoft Active Directory. Siga as instruções no [criar e modificar a configuração do peering](expressroute-circuit-peerings.md) artigo.


### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Iniciar sessão na sua conta do Azure

Antes de iniciar esta configuração, deve iniciar sessão na conta do Azure. O cmdlet pede-lhe as credenciais de início de sessão da conta Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell.

Abra a consola do PowerShell com privilégios elevados e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar a ligar. Se estiver a utilizar o Azure Cloud Shell, não precisa de executar este cmdlet, como irá automaticamente iniciar sessão.

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

## <a name="prefixes"></a>Passo 1: Obter uma lista de prefixos e valores de Comunidade do BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores de Comunidade do BGP

Utilize o cmdlet seguinte para obter a lista de valores de Comunidade do BGP associados aos serviços acessíveis através do peering da Microsoft e a lista de prefixos associadas a eles:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Fazer uma lista dos valores que pretende utilizar

Faça uma lista de valores de Comunidade do BGP que pretende utilizar no filtro de rota. Por exemplo, o valor de Comunidade do BGP para os serviços do Dynamics 365 é 12076:5040.

## <a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo "Permitir". Esta regra pode ter uma lista de valores de Comunidade do BGP associados a ele.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Primeiro, crie o filtro de rota. O comando 'New-AzRouteFilter' apenas cria um recurso de filtro de rota. Depois de criar o recurso, tem, em seguida, criar uma regra e anexá-lo para o objeto de filtro de rota. Execute o seguinte comando para criar um recurso de filtro de rota:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode especificar um conjunto de Comunidades BGP como uma lista separada por vírgulas, como mostrado no exemplo. Execute o seguinte comando para criar uma nova regra:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Adicionar a regra para o filtro de rota

Execute o seguinte comando para adicionar a regra de filtro para o filtro de rota:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Passo 3: Anexar o filtro de rota para um circuito do ExpressRoute

Execute o seguinte comando para anexar o filtro de rota ao circuito do ExpressRoute, pressupondo que tenha apenas peering da Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize os seguintes passos:

1. Execute o seguinte comando para obter o recurso de filtro de rota:

  ```azurepowershell-interactive
  $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Obter a rota regras do filtro para o recurso de filtro de rota ao executar o seguinte comando:

  ```azurepowershell-interactive
  $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está ligado a um circuito, atualizações para a lista de Comunidade do BGP propagarem automaticamente alterações de anúncio de prefixo adequado por meio de sessões BGP estabelecidas. Pode atualizar a lista de Comunidade do BGP de seu filtro de rota com o seguinte comando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Para anular a exposição de um filtro de rota de um circuito do ExpressRoute

Depois de um filtro de rota é desanexado do circuito ExpressRoute, não existem prefixos são anunciados através da sessão BGP. Pode anular a exposição de um filtro de rota de um circuito do ExpressRoute com o seguinte comando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Para eliminar um filtro de rota

Só é possível eliminar um filtro de rota, se não está ligado a qualquer circuito. Certifique-se de que o filtro de rota não está ligado a qualquer circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota com o seguinte comando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
