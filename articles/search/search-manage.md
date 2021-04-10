---
title: Administração de serviços no portal
titleSuffix: Azure Cognitive Search
description: Gerencie um serviço de Pesquisa Cognitiva Azure, um serviço de pesquisa em nuvem hospedado no Microsoft Azure, utilizando o portal Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579784"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administração de serviços para Azure Cognitive Search no portal Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [CLI do Azure](search-manage-azure-cli.md)
> * [API REST](/rest/api/searchmanagement/)
> * [SDK do .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Pitão](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A Azure Cognitive Search é um serviço de pesquisa totalmente gerido e baseado na nuvem usado para construir uma rica experiência de pesquisa em apps personalizadas. Este artigo cobre as tarefas administrativas que pode desempenhar no [portal Azure](https://portal.azure.com) para um serviço de pesquisa que já criou. O portal permite-lhe executar todas as [tarefas de gestão relacionadas](#management-tasks) com o serviço, bem como a gestão e exploração de conteúdos. Como tal, o portal proporciona um amplo acesso ao espectro a todos os aspetos das operações de serviço de busca.

Cada serviço de pesquisa é gerido como um recurso autónomo. A imagem a seguir mostra as páginas do portal para um único serviço de pesquisa gratuito chamado "demo-search-svc". Embora possa estar habituado a usar o Azure PowerShell ou o Azure CLI para gestão de serviços, faz sentido familiarizar-se com as ferramentas e capacidades que as páginas do portal fornecem. Algumas tarefas são mais fáceis e rápidas de executar no portal do que através do código. 

## <a name="overview-home-page"></a>Página geral (casa)

A página geral é a página "home" de cada serviço. Abaixo, as áreas no ecrã incluídas em caixas vermelhas indicam tarefas, ferramentas e azulejos que pode utilizar frequentemente, especialmente se for novo no serviço.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Páginas do portal para um serviço de pesquisa" border="true":::

| Área | Descrição |
|------|-------------|
| 1  | A secção **Essentials** mostra as propriedades de serviço, incluindo o ponto final utilizado na configuração de ligações. Também mostra que o nível, réplica e partição contam num ápice. |
| 2 | No topo da página estão uma série de comandos para invocar ferramentas interativas ou gerir o serviço. Tanto os [dados de importação como](search-get-started-portal.md) [o explorador de pesquisa](search-explorer.md) são comumente usados para prototipagem e exploração. |
| 3 | Abaixo da secção **Essentials,** encontram-se uma série de sub-páginas com placas para acesso rápido a estatísticas de utilização, métricas de saúde de serviço, e acesso a todos os índices, indexadores, fontes de dados e skillsets existentes no seu serviço. Se selecionar um índice ou outro objeto, as páginas adicionais ficam disponíveis para mostrar a composição, definições e estado do objeto (se aplicável). |
| 4 | À esquerda, pode aceder a links que abrem páginas adicionais para aceder às teclas API utilizadas nas ligações, configurar o serviço, configurar segurança, monitorizar operações, automatizar tarefas e obter suporte. |

### <a name="read-only-service-properties"></a>Propriedades de serviço apenas para leitura

Vários aspetos de um serviço de pesquisa são determinados quando o serviço é prestado e não podem ser alterados:

* Nome de serviço (não é possível renomear um serviço de pesquisa)
* Localização do serviço (não é possível mover facilmente um serviço de pesquisa intacto para outra região. Embora exista um modelo, mover o conteúdo é um processo manual)
* Nível de serviço (não é possível mudar de S1 para S2, por exemplo, sem criar um novo serviço)

## <a name="management-tasks"></a>Tarefas de gestão

A administração do serviço é leve por design, e é muitas vezes definida pelas tarefas que pode executar em relação ao próprio serviço:

* [Ajustar a capacidade](search-capacity-planning.md) adicionando ou removendo réplicas e divisórias
* [Gerir as chaves API utilizadas](search-security-api-keys.md) para operações de administração e consulta
* [Controlar o acesso às operações de administração](search-security-rbac.md) através de uma segurança baseada em funções
* [Configure as regras de firewall IP](service-configure-firewall.md) para restringir o acesso através do endereço IP
* [Configure um ponto final privado](service-create-private-endpoint.md) usando a Azure Private Link e uma rede virtual privada
* [Saúde do serviço de monitorização](search-monitor-usage.md): armazenamento, volumes de consulta e latência

Também pode enumerar todos os objetos criados no serviço: índices, indexadores, fontes de dados, skillsets, etc. A página geral do portal mostra-lhe todo o conteúdo que existe no seu serviço. A grande maioria das operações num serviço de pesquisa está relacionada com conteúdos.

As mesmas tarefas de gestão executadas no portal também podem ser tratadas programáticamente através do módulo [Management REST,](/rest/api/searchmanagement/) [Az.Search PowerShell,](search-manage-powershell.md) [módulo Az search Azure CLI](search-manage-azure-cli.md), e os SDKs Azure para .NET, Python, Java e JavaScript. As tarefas administrativas estão totalmente representadas em todo o portal e em todas as interfaces programáticas. Não existe uma tarefa administrativa específica que esteja disponível apenas numa modalidade.

A Cognitive Search aproveita outros serviços Azure para uma monitorização e gestão mais profundas. Por si só, os únicos dados armazenados dentro do serviço de pesquisa são o conteúdo de objetos (índices, indexantes e definições de fonte de dados, e outros objetos). As métricas reportadas às páginas do portal são retiradas de troncos internos num ciclo de 30 dias. Para retenção de registos controlada pelo utilizador e eventos adicionais, necessitará do [Azure Monitor](../azure-monitor/index.yml). Para obter mais informações sobre a configuração do registo de diagnóstico para um serviço de pesquisa, consulte [recolher e analisar dados de registo](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Permissões de administrador

Quando abre a página geral do serviço de pesquisa, as permissões atribuídas à sua conta determinam quais as páginas disponíveis para si. A página geral no início do artigo mostra as páginas do portal que um administrador ou colaborador irá ver.

No recurso Azure, os direitos administrativos são concedidos através de atribuições de funções. No contexto da Pesquisa Cognitiva Azure, as [atribuições](search-security-rbac.md) de funções determinarão quem pode alocar réplicas e divisórias ou gerir as teclas API, independentemente de estarem a utilizar o portal, [o PowerShell,](search-manage-powershell.md) [o Azure CLI,](search-manage-azure-cli.md)ou as [APIs de Rest de Gestão:](/rest/api/searchmanagement/search-howto-management-rest-api)

> [!TIP]
> O fornecimento ou desmantelamento do próprio serviço pode ser feito por um administrador ou coadministrador de subscrição da Azure. Utilizando mecanismos azures, pode bloquear uma subscrição ou recurso para evitar a eliminação acidental ou não não autorizada do seu serviço de pesquisa por utilizadores com direitos de administração. Para obter mais informações, consulte [os recursos de bloqueio para evitar a eliminação inesperada.](../azure-resource-manager/management/lock-resources.md)

## <a name="next-steps"></a>Passos seguintes

* Rever [capacidades de monitorização](search-monitor-usage.md) disponíveis no portal
* Automatize com [PowerShell](search-manage-powershell.md) ou [Azure CLI](search-manage-azure-cli.md)
* Rever [funcionalidades de segurança](search-security-overview.md) para proteger conteúdos e operações
* Permitir [a verificação de trabalhos](search-monitor-logs.md) de consulta e indexação de consultas