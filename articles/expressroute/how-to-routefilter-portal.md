---
title: 'ExpressRoute: Filtros de rota - Microsoft peering:Portal Azure'
description: Este artigo descreve como configurar filtros de rotas para a Microsoft a espreitar através do portal Azure.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037446"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configure filtros de rotas para o peering da Microsoft: Portal Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam-no a configurar e a gerir filtros de rotas para circuitos ExpressRoute.

Os serviços do Office 365, tais como Exchange Online, SharePoint Online e Skype for Business, e serviços Azure, como armazenamento e SQL DB, estão acessíveis através do peering da Microsoft. Quando o peering da Microsoft é configurado num circuito ExpressRoute, todos os prefixos relacionados com estes serviços são anunciados através das sessões de BGP que estão estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades de BGP.](expressroute-routing.md#bgp)

Se necessitar de conectividade com todos os serviços, um grande número de prefixos são anunciados através do BGP. Isto aumenta significativamente o tamanho das tabelas de rotas mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do peering da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas formas. Pode:

- Filtrar prefixos indesejados aplicando filtros de rotas nas comunidades de BGP. Esta é uma prática de networking padrão e é usada comumente em muitas redes.

- Defina filtros de rota e aplique-os no circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do peering da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rotas

Quando o peering da Microsoft está configurado no seu circuito ExpressRoute, os routers de borda da Microsoft estabelecem um par de sessões de BGP com os routers de borda (o seu ou o do seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de todos os valores comunitários do BGP que pretende permitir. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder anexar filtros de rota com os serviços do Office 365, deve ter autorização para consumir os serviços do Office 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços do Office 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para mais informações sobre o processo de autorização, consulte [o Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> O peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do peering da Microsoft, mesmo que os filtros de rota não estejam definidos. O peering da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para conseguir ligar-se com sucesso aos serviços através do peering da Microsoft, deve completar os seguintes passos de configuração:

- Deve ter um circuito expressRoute ativo que tenha o peering da Microsoft aprovisionado. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Crie um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de prosseguir. O circuito ExpressRoute deve estar num estado aprovisionado e habilitado.
  - [Crie o peering da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a fornecer à Microsoft o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identifique os serviços com que consumir através do peering da Microsoft
    - Identificar a lista dos valores comunitários do BGP associados aos serviços
    - Crie uma regra que permita a lista de prefixos correspondente aos valores comunitários do BGP

-  Deve ligar o filtro de rota ao circuito ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que cumpre os seguintes critérios:

 - Reveja os [pré-requisitos](expressroute-prerequisites.md) e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute deve estar num estado aprovisionado e habilitado.

 - Deve ter um microsoft ativo a espreitar. Siga as instruções na [Create e modificando a configuração de peering](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passo 1: Obtenha uma lista de prefixos e valores comunitários de BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista dos valores comunitários do BGP

Os valores comunitários do BGP associados aos serviços acessíveis através do peering da Microsoft estão disponíveis na página de requisitos de [encaminhamento ExpressRoute.](expressroute-routing.md)

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que pretende utilizar

Faça uma lista dos [valores comunitários](expressroute-routing.md#bgp) de BGP que pretende utilizar no filtro de rota. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários de BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota
Pode criar um filtro de rota selecionando a opção de criar um novo recurso. Clique em Criar um**Filtro**de Rota**de** > Rede de **recursos,** > como mostrado na seguinte imagem:

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Deve colocar o filtro de rota num grupo de recursos. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode adicionar e atualizar regras selecionando o separador de regras de gestão para o filtro de rota.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Pode selecionar os serviços a que pretende ligar a partir da lista de lançamentos e guardar a regra quando feito.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passo 3: Fixe o filtro de rota a um circuito ExpressRoute

Pode ligar o filtro de rota a um circuito selecionando o botão "adicionar circuito" e selecionando o circuito ExpressRoute da lista de drop-down.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se o fornecedor de conectividade configurar o seu circuito ExpressRoute, refresque o circuito a partir da lâmina do circuito ExpressRoute antes de selecionar o botão "adicionar circuito".

![Criar um filtro de rota](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Pode ver propriedades de um filtro de rota quando abrir o recurso no portal.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Pode atualizar a lista de valores comunitários de BGP anexados a um circuito selecionando o botão "Gerir a regra".


![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para separar um filtro de rota de um circuito ExpressRoute

Para separar um circuito do filtro de rota, clique à direita no circuito e clique em "dissociar".

![Criar um filtro de rota](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para eliminar um filtro de rota

Pode eliminar um filtro de rota selecionando o botão de eliminação. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passos Seguintes

* Para mais informações sobre o ExpressRoute, consulte o [ExpressRoute FAQ](expressroute-faqs.md).

* Para obter informações sobre amostras de configuração do router, consulte as amostras de [configuração do Router para configurar e gerir o encaminhamento](expressroute-config-samples-routing.md). 
