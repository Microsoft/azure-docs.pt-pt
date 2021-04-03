---
title: 'Tutorial: Configurar filtros de rota para o espreocupar a Microsoft - Portal Azure'
description: Este tutorial descreve como configurar filtros de rota para a Microsoft a espreitar através do portal Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109141"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Tutorial: Configurar filtros de rota para a Microsoft a espreitar utilizando o portal Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos deste artigo ajudam-no a configurar e a gerir filtros de rota para circuitos ExpressRoute.

Os serviços microsoft 365, como Exchange Online, SharePoint Online e Skype for Business, estão acessíveis através do perscíduo da Microsoft. Quando o espreitamento da Microsoft é configurado num circuito ExpressRoute, todos os prefixos relacionados com estes serviços são publicitados através das sessões de BGP que são estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades BGP.](expressroute-routing.md#bgp)

A conectividade com todos os serviços Azure e Microsoft 365 causa um grande número de prefixos é anunciado através do BGP. O grande número de prefixos aumenta significativamente o tamanho das tabelas de rota mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do espreitamento da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas maneiras. Pode:

* Filtrar prefixos indesejados aplicando filtros de rota nas comunidades de BGP. A filtragem de rotas é uma prática padrão de networking e é usada comumente dentro de muitas redes.

* Defina os filtros de rota e aplique-os no seu circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do espreitamento da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Obtenha valores comunitários BGP.
> - Crie a regra do filtro de rota e do filtro.
> - Filtro de rota associado a um circuito ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o espreguiçadamento da Microsoft é configurado no seu circuito ExpressRoute, os routers de borda da Microsoft estabelecem um par de sessões de BGP com os seus routers de borda através do seu fornecedor de conectividade. Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista permitida de todos os valores comunitários da BGP. Uma vez que um recurso de filtro de rota é definido e ligado a um circuito ExpressRoute, todos os prefixos que mapeam para os valores comunitários BGP são anunciados para a sua rede.

Para anexar filtros de rota com os serviços microsoft 365, tem de ter autorização para consumir os serviços microsoft 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços microsoft 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para obter mais informações sobre o processo de autorização, consulte [a Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 

## <a name="prerequisites"></a>Pré-requisitos

- Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

- Deve ter um circuito ExpressRoute ativo que tenha a Microsoft a espreitar. Pode utilizar as seguintes instruções para realizar estas tarefas:
  - [Crie um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.
  - [Crie o olho da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a provisão da Microsoft para o seu circuito.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Obtenha uma lista de prefixos e valores comunitários de BGP

### <a name="get-a-list-of-bgp-community-values"></a>Obtenha uma lista de valores comunitários de BGP

Os valores comunitários BGP associados a serviços acessíveis através do peering da Microsoft estão disponíveis na página de [requisitos de encaminhamento ExpressRoute.](expressroute-routing.md)

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Faça uma lista dos valores que pretende utilizar

Faça uma lista dos [valores comunitários BGP](expressroute-routing.md#bgp) que pretende utilizar no filtro de rota. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Crie um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários BGP associados a ela.

1. Selecione **Criar um recurso** e, em seguida, procurar o filtro *Route* como mostrado na seguinte imagem:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Screenshot que mostra a página do filtro Rota":::

1. Coloque o filtro de rota num grupo de recursos. Certifique-se de que a localização é igual ao circuito ExpressRoute. Selecione **'Rever + criar'** e, em seguida, **criar**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Screenshot que mostra a página do filtro de rota Create com valores de exemplo introduzidos":::

### <a name="create-a-filter-rule"></a>Criar uma regra de filtro

1. Para adicionar e atualizar as regras, selecione o separador regras de gestão para o filtro de rota.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Screenshot que mostra a página de visão geral com a ação de regra de Gestão em destaque":::

1. Selecione os serviços a que pretende ligar a partir da lista de drop-down e guarde a regra quando terminar.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Screenshot que mostra a janela de regras De gestão com serviços selecionados na lista de abandono das comunidades de serviços permitidos":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Fixe o filtro de rota a um circuito ExpressRoute

Fixe o filtro de rota a um circuito selecionando o botão **+ Add Circuit** e selecionando o circuito ExpressRoute a partir da lista de queda.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Screenshot que mostra a página de visão geral com a ação do circuito Add selecionada":::

Se o fornecedor de conectividade configurar o seu circuito ExpressRoute, refresque o circuito a partir da página do circuito ExpressRoute antes de selecionar o botão **+ Add Circuit.**

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Screenshot que mostra a página de visão geral com a ação Refresh selecionada.":::

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Pode ver as propriedades de um filtro de rota quando abre o recurso no portal.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Captura de ecrã a mostrar a página Descrição geral":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

1. Pode atualizar a lista de valores comunitários BGP anexados a um circuito selecionando o botão **de regra 'Gerir'.**

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Atualizar filtros de rota com a ação de regra de gestão":::

1. Selecione as comunidades de serviço que deseja e, em seguida, **selecione Guardar**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Screenshot que mostra a janela de regras 'Gerir' com serviços selecionados":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para separar um filtro de rota de um circuito ExpressRoute

Para separar um circuito do filtro de rota, clique com o botão direito no circuito e selecione **Disassociate**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Screenshot que mostra a página de visão geral com a ação dissociada em destaque":::


## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar um filtro de rota selecionando o botão **Eliminar.** Certifique-se de que o filtro Rota não está associado a nenhum circuito antes de o fazer.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Excluir um filtro de rota":::

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre amostras de configuração do router, consulte:

> [!div class="nextstepaction"]
> [Amostras de configuração do router para configurar e gerir o encaminhamento](expressroute-config-samples-routing.md)
