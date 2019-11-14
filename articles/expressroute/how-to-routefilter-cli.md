---
title: 'ExpressRoute: filtros de rota-emparelhamento da Microsoft: CLI do Azure'
description: Este artigo descreve como configurar filtros de rota para Peering da Microsoft com a CLI do Azure
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033417"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configurar filtros de rota para peering da Microsoft: CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos neste artigo ajudam-na configurar e gerir filtros de rota para circuitos do ExpressRoute.

Os serviços do Office 365, como o Exchange Online, o SharePoint Online e o Skype for Business, podem ser acessados por meio do emparelhamento da Microsoft. Quando o peering da Microsoft é configurado num circuito do ExpressRoute, relacionadas com estes serviços de todos os prefixos são anunciados através as sessões de BGP estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista de valores das Comunidades do BGP e os serviços podem ser mapeados para, consulte [Comunidades do BGP](expressroute-routing.md#bgp).

Se precisar de uma conectividade a todos os serviços, um grande número de prefixos é anunciado através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantido por routers na sua rede. Se planear consumir apenas um subconjunto de serviços oferecidos através do peering da Microsoft, pode reduzir o tamanho de suas tabelas de rota de duas formas. Pode:

* Filtre prefixos indesejados ao aplicar filtros de rota em Comunidades BGP. Esta é uma prática padrão de rede e é usada normalmente muitas redes.

* Definir filtros de rota e aplicá-las para o seu circuito do ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que pretende consumir através do peering da Microsoft. Routers de ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about"></a>Sobre filtros de rota

Se o peering da Microsoft é configurado no seu circuito do ExpressRoute, os routers de limite de Microsoft estabelecem um par de sessões BGP com os routers de limite (sua ou seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista de permissões de todos os valores da comunidade BGP. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder-se de que filtros de rota com serviços do Office 365 nos mesmos de anexar, tem de ter autorização para consumir os serviços do Office 365 através do ExpressRoute. Se não está autorizado a consumir os serviços do Office 365 através do ExpressRoute, a operação para anexar os filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Peering da Microsoft dos circuitos do ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através de peering, da Microsoft, mesmo se os filtros de rota não estão definidos. Peering da Microsoft dos circuitos do ExpressRoute que estão configurados em ou depois de 1 de Agosto de 2017 não terão qualquer prefixos anunciados até que um filtro de rota é anexado ao circuito.
> 
> 

### <a name="workflow"></a>Workflow

Para poder ligar com êxito aos serviços através do peering da Microsoft, tem de concluir os seguintes passos de configuração:

* Tem de ter um circuito ExpressRoute ativo que tenha aprovisionado de peering da Microsoft. Pode utilizar as seguintes instruções para realizar estas tarefas:
  * [Criar um circuito do ExpressRoute](howto-circuit-cli.md) e ter o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.
  * [Criar o peering da Microsoft](howto-routing-cli.md) se gerir diretamente a sessão de BGP. Ou, ter o seu fornecedor de conectividade aprovisionar o peering da Microsoft para o seu circuito.

* Tem de criar e configurar um filtro de rota.
  * Identificar os serviços que com a consumir através do peering da Microsoft
  * Identificar a lista de valores de Comunidade do BGP associado aos serviços
  * Criar uma regra para permitir que a lista de prefixo que corresponda os valores de Comunidade do BGP

* Terá de anexar o filtro de rota ao circuito do ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](howto-circuit-cli.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito do ExpressRoute tem de estar num Estado aprovisionado e ativado.

* Tem de ter um peering da Microsoft Active Directory. Siga as instruções apresentadas em [criar e modificar a configuração de peering](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição

Para iniciar a configuração, inicie sessão na sua conta do Azure. Se estiver a utilizar o "experimente-", que tem sessão iniciada automaticamente e pode ignorar o passo de início de sessão. Utilize os exemplos seguintes para ajudar na ligação:

```azurecli
az login
```

Verifique as subscrições da conta.

```azurecli-interactive
az account list
```

Selecione a subscrição para o qual pretende criar um circuito do ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Passo 1: Obter uma lista de prefixos e valores de Comunidade do BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. obter uma lista de valores de comunidade BGP

Utilize o cmdlet seguinte para obter a lista de valores de Comunidade do BGP associados aos serviços acessíveis através do peering da Microsoft e a lista de prefixos associadas a eles:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. faça uma lista dos valores que você deseja usar

Faça uma lista de valores de Comunidade do BGP que pretende utilizar no filtro de rota.

## <a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra e, a regra tem de ser do tipo "Permitir". Esta regra pode ter uma lista de valores de Comunidade do BGP associados a ele.

### <a name="1-create-a-route-filter"></a>1. criar um filtro de rota

Primeiro, crie o filtro de rota. O comando `az network route-filter create` cria apenas um recurso de filtro de rota. Depois de criar o recurso, tem, em seguida, criar uma regra e anexá-lo para o objeto de filtro de rota. Execute o seguinte comando para criar um recurso de filtro de rota:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. criar uma regra de filtro

Execute o seguinte comando para criar uma nova regra:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Passo 3: Ligar o filtro de rota para um circuito do ExpressRoute

Execute o seguinte comando para anexar o filtro de rota ao circuito do ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Tarefas comuns

### <a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize o seguinte comando:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está ligado a um circuito, atualizações para a lista de Comunidade do BGP propagarem automaticamente alterações de anúncio de prefixo adequado por meio de sessões BGP estabelecidas. Pode atualizar a lista de Comunidade do BGP de seu filtro de rota com o seguinte comando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Para anular a exposição de um filtro de rota de um circuito do ExpressRoute

Depois de um filtro de rota é desanexado do circuito ExpressRoute, não existem prefixos são anunciados através da sessão BGP. Pode anular a exposição de um filtro de rota de um circuito do ExpressRoute com o seguinte comando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Para eliminar um filtro de rota

Só é possível eliminar um filtro de rota, se não está ligado a qualquer circuito. Certifique-se de que o filtro de rota não está ligado a qualquer circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota com o seguinte comando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
