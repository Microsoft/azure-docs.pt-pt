---
title: 'ExpressRoute: Filtros de rota - Microsoft peering:Azure CLI'
description: Este artigo descreve como configurar filtros de rota para o Microsoft Peering usando O Azure CLI
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fbce15b84371b7b7907deff361e2a2e706bec28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567712"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configure filtros de rota para o estoiramento da Microsoft: Azure CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Os filtros de rota são uma forma de consumir um subconjunto de serviços suportados através do peering da Microsoft. Os passos deste artigo ajudam-no a configurar e a gerir filtros de rota para circuitos ExpressRoute.

Os serviços microsoft 365, como Exchange Online, SharePoint Online e Skype for Business, estão acessíveis através do perscíduo da Microsoft. Quando o espreitamento da Microsoft é configurado num circuito ExpressRoute, todos os prefixos relacionados com estes serviços são anunciados através das sessões de BGP que são estabelecidas. Está anexado um valor da comunidade BGP a cada prefixo para identificar o serviço oferecido através do prefixo. Para obter uma lista dos valores comunitários do BGP e dos serviços a que mapeiam, consulte [as comunidades BGP.](expressroute-routing.md#bgp)

Se necessitar de conectividade a todos os serviços, um grande número de prefixos são anunciados através do BGP. Isto aumenta significativamente o tamanho das tabelas de rota mantidas pelos routers dentro da sua rede. Se planeia consumir apenas um subconjunto de serviços oferecidos através do espreitamento da Microsoft, pode reduzir o tamanho das suas tabelas de rotas de duas maneiras. Pode:

* Filtrar prefixos indesejados aplicando filtros de rota nas comunidades de BGP. Esta é uma prática padrão de networking e é usada comumente em muitas redes.

* Defina os filtros de rota e aplique-os no seu circuito ExpressRoute. Um filtro de rota é um novo recurso que permite selecionar a lista de serviços que planeia consumir através do espreitamento da Microsoft. Os routers ExpressRoute apenas enviam a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o espreguiçadeira da Microsoft está configurado no seu circuito ExpressRoute, os routers de borda da Microsoft estabelecem um par de sessões de BGP com os routers de borda (o seu ou o do seu fornecedor de conectividade). Não são anunciadas rotas à sua rede. Para ativar anúncios de rota para a sua rede, tem de associar um filtro de rota.

Um filtro de rota permite-lhe identificar os serviços que deseja consumir através do peering da Microsoft do circuito do ExpressRoute. É essencialmente uma lista permitida de todos os valores comunitários da BGP. Depois de um recurso de filtro de rota ser definido e anexado a um circuito do ExpressRoute, todos os prefixos que mapeiam para os valores da comunidade BGP são anunciados à sua rede.

Para poder anexar filtros de rota com os serviços Microsoft 365, tem de ter autorização para consumir os serviços da Microsoft 365 através do ExpressRoute. Se não estiver autorizado a consumir os serviços microsoft 365 através do ExpressRoute, a operação para anexar filtros de rota falha. Para obter mais informações sobre o processo de autorização, consulte [a Azure ExpressRoute para a Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> O perspitamento da Microsoft nos circuitos ExpressRoute que foram configurados antes de 1 de agosto de 2017 terá todos os prefixos de serviço anunciados através do olho da Microsoft, mesmo que os filtros de rota não estejam definidos. O perspitamento da Microsoft dos circuitos ExpressRoute que estejam configurados em ou depois de 1 de agosto de 2017 não terá quaisquer prefixos anunciados até que um filtro de rota seja ligado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para conseguir ligar-se com sucesso aos serviços através do estomamento da Microsoft, tem de completar os seguintes passos de configuração:

* Deve ter um circuito ExpressRoute ativo que tenha a Microsoft a espreitar. Pode utilizar as seguintes instruções para realizar estas tarefas:
  * [Crie um circuito ExpressRoute](howto-circuit-cli.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de prosseguir. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.
  * [Crie o olho da Microsoft](howto-routing-cli.md) se gerir a sessão de BGP diretamente. Ou, tenha o seu fornecedor de conectividade a provisão da Microsoft para o seu circuito.

* Tem de criar e configurar um filtro de rota.
  * Identifique os serviços com que consome através do espremo da Microsoft
  * Identificar a lista de valores comunitários BGP associados aos serviços
  * Crie uma regra que permita que a lista de prefixos corresponda aos valores da comunidade BGP

* Deve fixar o filtro de rota ao circuito ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [Criar um circuito ExpressRoute](howto-circuit-cli.md) e solicite ao seu fornecedor de conectividade para ativar o circuito antes de continuar. O circuito ExpressRoute deve estar num estado a provisionado e habilitado.

* Deve ter um Microsoft ativo a espreitar. Siga as instruções na [Configuração de Espreitamento e modificação da configuração de peering](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição

Para iniciar a sua configuração, inicie sação na sua conta Azure. Se estiver a utilizar o "Try It", é assinado automaticamente e pode saltar o passo de login. Utilize os seguintes exemplos para o ajudar a ligar:

```azurecli
az login
```

Verifique as subscrições da conta.

```azurecli-interactive
az account list
```

Selecione a subscrição para a qual pretende criar um circuito ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passo 1: Obtenha uma lista de prefixos e valores comunitários de BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenha uma lista dos valores comunitários do BGP

Utilize o cmdlet seguinte para obter a lista de valores comunitários BGP associados a serviços acessíveis através do estomamento da Microsoft, e a lista de prefixos associados a eles:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que pretende utilizar

Faça uma lista dos valores comunitários BGP que pretende utilizar no filtro de rota.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passo 2: Criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, e a regra deve ser do tipo 'Permitir'. Esta regra pode ter uma lista de valores comunitários BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Criar um filtro de rota

Primeiro, crie o filtro de rota. O comando `az network route-filter create` apenas cria um recurso de filtro de rota. Depois de criar o recurso, deve então criar uma regra e fixá-la ao objeto do filtro de rota. Executar o seguinte comando para criar um recurso de filtro de rota:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Criar uma regra de filtro

Executar o seguinte comando para criar uma nova regra:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passo 3: Fixe o filtro de rota a um circuito ExpressRoute

Executar o seguinte comando para ligar o filtro de rota ao circuito ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, utilize o seguinte comando:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já estiver ligado a um circuito, as atualizações à lista comunitária BGP propagam automaticamente as alterações de anúncios de prefixo apropriados através das sessões de BGP estabelecidas. Pode atualizar a lista comunitária BGP do seu filtro de rota utilizando o seguinte comando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para separar um filtro de rota de um circuito ExpressRoute

Uma vez que um filtro de rota é desligado do circuito ExpressRoute, nenhum prefixo é anunciado através da sessão BGP. Pode retirar um filtro de rota de um circuito ExpressRoute utilizando o seguinte comando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para eliminar um filtro de rota

Só pode apagar um filtro de rota se não estiver ligado a nenhum circuito. Certifique-se de que o filtro de rota não está ligado a nenhum circuito antes de tentar eliminá-lo. Pode eliminar um filtro de rota utilizando o seguinte comando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)
