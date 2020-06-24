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
ms.date: 11/04/2019
ms.openlocfilehash: f91df2e4b76e2a85705100fa5626877b9a86312d
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752580"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administração de serviços para Azure Cognitive Search no portal Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Pitão](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A Azure Cognitive Search é um serviço de pesquisa totalmente gerido e baseado na nuvem usado para construir uma rica experiência de pesquisa em apps personalizadas. Este artigo cobre as tarefas de administração de serviços que pode desempenhar no [portal Azure](https://portal.azure.com) para um serviço de pesquisa que já forjado. A administração de serviços é leve por design, limitada às seguintes tarefas:

> [!div class="checklist"]
> * Gerir o acesso às *teclas api utilizadas* para ler ou escrever acesso ao seu serviço.
> * Ajuste a capacidade de serviço alterando a atribuição de divisórias e réplicas.
> * Monitorize a utilização do recurso, em relação aos limites máximos do seu nível de serviço.

Note que *a atualização* não está listada como uma tarefa administrativa. Como os recursos são atribuídos quando o serviço é prestado, passar para um nível diferente requer um novo serviço. Para mais detalhes, consulte [Criar um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md).

Pode monitorizar o volume de consultas e outras métricas e utilizar esses insights para ajustar o seu serviço para tempos de resposta mais rápidos. Para obter mais informações, consulte [as métricas de utilização e consulta](search-monitor-usage.md) do Monitor e o Desempenho e [otimização.](search-performance-optimization.md)

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
O fornecimento ou desmantelamento do próprio serviço pode ser feito por um administrador ou coadministrador de subscrição da Azure.

Dentro de um serviço, qualquer pessoa com acesso ao URL de serviço e uma chave api-administrador tem acesso de leitura-escrita ao serviço. O acesso à escrita de leitura proporciona a capacidade de adicionar, eliminar ou modificar objetos de servidor, incluindo chaves api, índices, indexadores, fontes de dados, horários e atribuições de funções implementadas através [de funções definidas pelo RBAC.](search-security-rbac.md)

Toda a interação do utilizador com a Azure Cognitive Search insere-se num destes modos: o acesso de leitura-escrita ao serviço (direitos de administrador) ou acesso apenas de leitura ao serviço (direitos de consulta). Para obter mais informações, consulte [Gerir as teclas api.](search-security-api-keys.md)

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Informação de registo e sistema
A Azure Cognitive Search não expõe ficheiros de registo para um serviço individual, quer através do portal, quer através de interfaces programáticas. No nível básico e acima, a Microsoft monitoriza todos os serviços de Pesquisa Cognitiva Azure para uma disponibilidade de 99,9% por acordos de nível de serviço (SLA). Se o serviço for lento ou o pedido de saída ficar abaixo dos limiares de SLA, as equipas de suporte analisam os ficheiros de registo disponíveis e resolvem o problema.

Em termos de informação geral sobre o seu serviço, pode obter informações das seguintes formas:

* No portal, no painel de atendimento, através de notificações, propriedades e mensagens de estado.
* Utilizar [o PowerShell](search-manage-powershell.md) ou a [API Management REST](https://docs.microsoft.com/rest/api/searchmanagement/) para obter propriedades de [serviço,](https://docs.microsoft.com/rest/api/searchmanagement/services)ou estado na utilização de recursos indexados.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos
No painel de instrumentos, a monitorização dos recursos limita-se às informações apresentadas no painel de serviço e a algumas métricas que pode obter consultando o serviço. No painel de instrumentos de serviço, na secção Utilização, pode determinar rapidamente se os níveis de recursos de partição são adequados para a sua aplicação. Pode obter recursos externos, como a monitorização do Azure, se quiser capturar e persistir em eventos registados. Para obter mais informações, consulte [Monitoring Azure Cognitive Search](search-monitor-usage.md).

Utilizando o serviço de pesquisa REST API, pode obter uma contagem de documentos e índices programáticamente: 

* [Obter Estatísticas de Índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Documentos de Contagem](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastres e interrupções de serviço

Embora possamos resgatar os seus dados, a Azure Cognitive Search não fornece falha instantânea do serviço se houver uma falha no nível do cluster ou do centro de dados. Se um cluster falhar no centro de dados, a equipa de operações detetará e trabalhará para restaurar o serviço. Você vai experimentar tempo de paragem durante a restauração do serviço, mas você pode solicitar créditos de serviço para compensar a indisponibilidade de serviço de acordo com o [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessário um serviço contínuo em caso de falhas catastróficas fora do controlo da Microsoft, poderá [providenciar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de geo-replicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que usam [indexantes](search-indexer-overview.md) para preencher e atualizar índices podem lidar com a recuperação de desastres através de indexadores geo-específicos alavancando a mesma fonte de dados. Dois serviços em diferentes regiões, cada um com um indexante, poderiam indexar a mesma fonte de dados para alcançar a geo-redundância. Se estiver a indexar a partir de fontes de dados que também são geo-redundantes, esteja ciente de que os indexantes de Pesquisa Cognitiva do Azure só podem realizar indexação incremental (fundindo atualizações de documentos novos, modificados ou eliminados) a partir de réplicas primárias. Num evento de failover, certifique-se de voltar a apontar o indexante para a nova réplica primária. 

Se não utilizar indexadores, utilizará o seu código de aplicação para empurrar objetos e dados para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [Performance e otimização na Pesquisa Cognitiva Azure.](search-performance-optimization.md)

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Como a Azure Cognitive Search não é uma solução primária de armazenamento de dados, não fornecemos um mecanismo formal de backup e restauro de autosserviço. No entanto, pode utilizar o código de amostra **de backup-backup** neste [Azure Cognitive Search .NET sample repo](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup da sua definição de índice e instantâneo para uma série de ficheiros JSON, e depois usar estes ficheiros para restaurar o índice, se necessário. Esta ferramenta também pode mover índices entre os níveis de serviço.

Caso contrário, o seu código de aplicação utilizado para criar e povoar um índice é a opção de restauro de facto se eliminar um índice por engano. Para reconstruir um índice, apagar-se-ia (assumindo que existe), recriaria o índice no serviço e recarregava-se recuperando dados da sua loja de dados primário.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Cada serviço de pesquisa começa com um mínimo de uma réplica e uma divisória. Se se inscreveu para um [nível que fornece recursos dedicados,](search-limits-quotas-capacity.md)clique no azulejo **SCALE** no painel de serviço para ajustar a utilização do recurso.

Quando adiciona capacidade através de qualquer recurso, o serviço utiliza-os automaticamente. Não são necessárias mais medidas da sua parte, mas há um ligeiro atraso antes do impacto do novo recurso ser realizado. Pode levar 15 minutos ou mais para a disponibilização de recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
Aumentar as consultas por segundo (QPS) ou alcançar uma elevada disponibilidade é feito adicionando réplicas. Cada réplica tem uma cópia de um índice, pelo que adicionar mais uma réplica traduz-se em mais um índice disponível para lidar com pedidos de consulta de serviço. São necessárias no mínimo 3 réplicas para uma elevada disponibilidade (ver [Planeamento da Capacidade](search-capacity-planning.md) para mais detalhes).

Um serviço de pesquisa com mais réplicas pode carregar pedidos de consulta de equilíbrio sobre um maior número de índices. Dado o nível de volume de consulta, a produção de consulta será mais rápida quando houver mais cópias do índice disponível para o serviço do pedido. Se estiver a sentir latência de consulta, pode esperar um impacto positivo no desempenho uma vez que as réplicas adicionais estejam online.

Embora a produção de consulta suba à medida que adiciona réplicas, não precisamente o dobro ou o triplo à medida que adiciona réplicas ao seu serviço. Todas as aplicações de pesquisa estão sujeitas a fatores externos que podem influenciar o desempenho da consulta. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta da consulta.

### <a name="add-partitions"></a>Adicionar divisórias
É mais comum adicionar réplicas, mas quando o armazenamento é limitado, pode adicionar divisórias para obter mais capacidade. O nível a que adquirou o serviço determina se as divisórias podem ser adicionadas. O nível básico está bloqueado numa partição. Os níveis padrão e acima suportam divisórias adicionais.

As divisórias são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefacto de cacos. Um índice é criado em 12 fragmentos, que podem ser armazenados em 1 divisória ou igualmente divididos em 2, 3, 4, 6 ou 12 divisórias (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas
Após períodos de elevados volumes de consultas, pode utilizar o slider para reduzir as réplicas após a normalização das cargas de consulta de pesquisa (por exemplo, após o fim das vendas de férias). Não são necessários mais passos da sua parte. A redução da contagem de réplicas renuncia às máquinas virtuais no centro de dados. As suas operações de consulta e ingestão de dados passarão a funcionar com menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover divisórias
Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode ter algum trabalho a fazer se estiver usando mais armazenamento do que pode ser reduzido. Por exemplo, se a sua solução estiver a utilizar três divisórias, a redução para uma ou duas divisórias gerará um erro se o novo espaço de armazenamento for inferior ao necessário para hospedar o seu índice. Como seria de esperar, as suas escolhas são eliminar índices ou documentos dentro de um índice associado para libertar espaço ou manter a configuração atual.

Não existe um método de deteção que lhe diga quais os fragmentos de índice armazenados em divisórias específicas. Cada divisória fornece aproximadamente 25 GB de armazenamento, por isso terá de reduzir o armazenamento para um tamanho que pode ser acomodado pelo número de divisórias que tem. Se quiser voltar a uma partição, todos os 12 fragmentos terão de caber.

Para ajudar no planeamento futuro, pode querer verificar o armazenamento (usando [as estatísticas do Índice Get](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver quanto realmente utilizou. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes
Uma vez que compreenda os conceitos por trás da administração do serviço, considere usar [o PowerShell](search-manage-powershell.md) para automatizar tarefas.

Recomendamos também a revisão do [artigo de desempenho e otimização.](search-performance-optimization.md)

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



