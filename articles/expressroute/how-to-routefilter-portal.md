---
title: 'Configurar filtros de rota para o emparelhamento da Microsoft: Azure ExpressRoute-Portal | Microsoft Docs'
description: Este artigo descreve como configurar filtros de rota para peering da Microsoft com o portal do Azure.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: c49b1fa1e2e8421146f5d5012de983c14934c23c
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122944"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurar filtros de rota para o emparelhamento da Microsoft: Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam-na configurar e gerir filtros de rota para circuitos do ExpressRoute.

Os serviços do Office 365, como o Exchange Online, o SharePoint Online e o Skype for Business, e os serviços do Azure, como o armazenamento e o SQL DB, podem ser acessados por meio do emparelhamento da Microsoft. Quando o peering da Microsoft é configurado num circuito do ExpressRoute, relacionadas com estes serviços de todos os prefixos são anunciados através as sessões de BGP estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista de valores das Comunidades do BGP e os serviços podem ser mapeados para, consulte [Comunidades do BGP](expressroute-routing.md#bgp).

Se precisar de uma conectividade a todos os serviços, um grande número de prefixos é anunciado através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantido por routers na sua rede. Se planear consumir apenas um subconjunto de serviços oferecidos através do peering da Microsoft, pode reduzir o tamanho de suas tabelas de rota de duas formas. Pode:

- Filtre prefixos indesejados ao aplicar filtros de rota em Comunidades BGP. Esta é uma prática padrão de rede e é usada normalmente muitas redes.

- Definir filtros de rota e aplicá-las para o seu circuito do ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que pretende consumir através do peering da Microsoft. Routers de ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre filtros de rota

Se o peering da Microsoft é configurado no seu circuito do ExpressRoute, os routers de limite de Microsoft estabelecem um par de sessões BGP com os routers de limite (sua ou seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de todos os valores de comunidade BGP que você deseja permitir. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder-se de que filtros de rota com serviços do Office 365 nos mesmos de anexar, tem de ter autorização para consumir os serviços do Office 365 através do ExpressRoute. Se não está autorizado a consumir os serviços do Office 365 através do ExpressRoute, a operação para anexar os filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito.
> 
> 

### <a name="workflow"></a>Workflow

Para poder ligar com êxito aos serviços através do peering da Microsoft, tem de concluir os seguintes passos de configuração:

- Tem de ter um circuito ExpressRoute ativo que tenha aprovisionado de peering da Microsoft. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e ter o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.
  - [Criar o peering da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se gerir diretamente a sessão de BGP. Ou, ter o seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

-  Tem de criar e configurar um filtro de rota.
    - Identificar os serviços que com a consumir através do peering da Microsoft
    - Identificar a lista de valores de Comunidade do BGP associado aos serviços
    - Criar uma regra para permitir que a lista de prefixo que corresponda os valores de Comunidade do BGP

-  Terá de anexar o filtro de rota ao circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que cumpre os seguintes critérios:

 - Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

 - Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.

 - Tem de ter um peering da Microsoft Active Directory. Siga as instruções apresentadas em [criar e modificar a configuração de peering](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Etapa 1: Obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obter uma lista de valores de Comunidade do BGP

Valores de Comunidade do BGP associados aos serviços acessíveis através do peering da Microsoft está disponível na [requisitos de encaminhamento do ExpressRoute](expressroute-routing.md) página.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Fazer uma lista dos valores que pretende utilizar

Faça uma lista de [valores de comunidade BGP](expressroute-routing.md#bgp) que você deseja usar no filtro de rota. 

## <a name="filter"></a>Etapa 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo "Permitir". Esta regra pode ter uma lista de valores de Comunidade do BGP associados a ele.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota
Pode criar um filtro de rota ao selecionar a opção para criar um novo recurso. Clique em **criar um recurso** > **rede** > **RouteFilter**, conforme mostrado na imagem seguinte:

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Tem de colocar o filtro de rota num grupo de recursos. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Pode adicionar e atualizar as regras, selecionando o guia de regra de gerenciar para o filtro de rota.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Você pode selecionar os serviços aos quais deseja se conectar na lista suspensa e salvar a regra quando terminar.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Etapa 3: Anexar o filtro de rota a um circuito de ExpressRoute

Você pode anexar o filtro de rota a um circuito selecionando o botão "Adicionar circuito" e selecionando o circuito do ExpressRoute na lista suspensa.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se o fornecedor de conectividade configura o peering para a sua atualização de circuito do ExpressRoute, o circuito a partir do painel de circuito do ExpressRoute antes de selecionar o botão "adicionar o circuito".

![Criar um filtro de rota](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Pode ver as propriedades de um filtro de rota ao abrir o recurso no portal.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Pode atualizar a lista de valores de Comunidade do BGP anexados a um circuito ao selecionar o botão "Gerir regra".


![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Para anular a exposição de um filtro de rota de um circuito do ExpressRoute

Para desanexar um circuito do filtro de rota, clique com o botão direito do mouse no circuito e clique em "desassociar".

![Criar um filtro de rota](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Para eliminar um filtro de rota

Pode eliminar um filtro de rota ao selecionar o botão de eliminação. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Próximos Passos

* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).

* Para obter informações sobre exemplos de configuração do roteador, consulte [exemplos de configuração do roteador para configurar e gerenciar o roteamento](expressroute-config-samples-routing.md). 
