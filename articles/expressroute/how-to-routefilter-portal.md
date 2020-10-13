---
title: 'ExpressRoute: Filtros de rota - Microsoft peering:Azure portal'
description: Este artigo descreve como configurar filtros de rota para a Microsoft a espreitar através do portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 468c7a0113a4603f4f47bb529145261ff50d96d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650549"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configure filtros de rota para o espreocupar a Microsoft: Portal Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos deste artigo ajudam-no a configurar e a gerir filtros de rota para circuitos ExpressRoute.

Os serviços microsoft 365, tais como Exchange Online, SharePoint Online e Skype for Business, e serviços Azure, como armazenamento e DB SQL, estão acessíveis através do espreitamento da Microsoft. Quando o espreitamento da Microsoft é configurado num circuito ExpressRoute, todos os prefixos relacionados com estes serviços são anunciados através das sessões de BGP que são estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades BGP.](expressroute-routing.md#bgp)

Se necessitar de conectividade a todos os serviços, um grande número de prefixos são anunciados através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do espreitamento da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas maneiras. Pode:

- Filtrar prefixos indesejados aplicando filtros de rota nas comunidades de BGP. Esta é uma prática padrão de networking e é usada comumente em muitas redes.

- Defina os filtros de rota e aplique-os no seu circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do espreitamento da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o espreguiçadeira da Microsoft está configurado no seu circuito ExpressRoute, os routers de borda da Microsoft estabelecem um par de sessões de BGP com os routers de borda (o seu ou o do seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de todos os valores comunitários BGP que você deseja permitir. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder anexar filtros de rota com os serviços Microsoft 365, tem de ter autorização para consumir os serviços da Microsoft 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços microsoft 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para obter mais informações sobre o processo de autorização, consulte [a Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para conseguir ligar-se com sucesso aos serviços através do estomamento da Microsoft, tem de completar os seguintes passos de configuração:

- Deve ter um circuito ExpressRoute ativo que tenha a Microsoft a espreitar. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Crie um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de prosseguir. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.
  - [Crie o olho da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a provisão da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identifique os serviços com que consome através do espremo da Microsoft
    - Identificar a lista de valores comunitários BGP associados aos serviços
    - Crie uma regra que permita que a lista de prefixos corresponda aos valores da comunidade BGP

-  Deve fixar o filtro de rota ao circuito ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que cumpre os seguintes critérios:

 - Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.

 - Deve ter um Microsoft ativo a espreitar. Siga as instruções na [Configuração de Espreitamento e modificação da configuração de peering](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passo 1: Obtenha uma lista de prefixos e valores comunitários de BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenha uma lista dos valores comunitários do BGP

Os valores comunitários BGP associados a serviços acessíveis através do peering da Microsoft estão disponíveis na página de [requisitos de encaminhamento ExpressRoute.](expressroute-routing.md)

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que pretende utilizar

Faça uma lista dos [valores comunitários BGP](expressroute-routing.md#bgp) que pretende utilizar no filtro de rota. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota
Pode criar um filtro de rota selecionando a opção de criar um novo recurso. Clique **em Criar um**  >  RouteFilter de rede**de**  >  **RouteFilter**recursos, como mostra a seguinte imagem:

![Screenshot que mostra a página "Filtro rota".](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Deve colocar o filtro de rota num grupo de recursos. 

![Screenshot que mostra a página "Criar filtro de rota" com valores de exemplo introduzidos.](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode adicionar e atualizar as regras selecionando o separador de regras de gestão para o filtro de rota.

![Screenshot que mostra a página "Visão Geral" com a ação "Gerir a regra" realçada.](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Pode selecionar os serviços a que pretende ligar a partir da lista de drop-down e guardar a regra quando terminar.

![Screenshot que mostra a janela "Gerir a regra" com serviços selecionados na lista de abandono das "comunidades de serviços permitidos".](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passo 3: Fixe o filtro de rota a um circuito ExpressRoute

Pode ligar o filtro de rota a um circuito selecionando o botão "add Circuit" e selecionando o circuito ExpressRoute a partir da lista de recuo.

![Screenshot que mostra a página "Visão Geral" com a ação "Adicionar circuito" selecionada.](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se o fornecedor de conectividade configurar o seu circuito ExpressRoute, refresque o circuito a partir da lâmina do circuito ExpressRoute antes de selecionar o botão "adicionar circuito".

![Screenshot que mostra a página "Visão Geral" com a ação "Refresh" selecionada.](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Pode ver as propriedades de um filtro de rota quando abre o recurso no portal.

![Screenshot que mostra a página "Visão Geral".](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Pode atualizar a lista de valores comunitários BGP anexados a um circuito selecionando o botão "Gerir a regra".


![Screenshot que mostra a página "Visão Geral" com a ação "Gerir a regra" realçada.](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Screenshot que mostra a janela "Gerir a regra" com serviços selecionados.](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para separar um filtro de rota de um circuito ExpressRoute

Para separar um circuito do filtro de rota, clique com o botão direito no circuito e clique em "desassociar".

![Screenshot que mostra a página "Visão Geral" com a ação "Dissociate" em destaque.](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para eliminar um filtro de rota

Pode eliminar um filtro de rota selecionando o botão eliminar. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)

* Para obter informações sobre as amostras de configuração do router, consulte [as amostras de configuração do Router para configurar e gerir o encaminhamento](expressroute-config-samples-routing.md). 
