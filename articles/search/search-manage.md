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
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282929"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administração de serviços para pesquisa cognitiva Azure no portal Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Pitão](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A Azure Cognitive Search é um serviço de pesquisa totalmente gerido e baseado na nuvem usado para construir uma rica experiência de pesquisa em aplicações personalizadas. Este artigo cobre as tarefas de administração de serviços que pode executar no [portal Azure](https://portal.azure.com) para um serviço de pesquisa que já disponibilizou. A administração de serviços é leve por design, limitada às seguintes tarefas:

> [!div class="checklist"]
> * Gerencie o acesso às *teclas api utilizadas* para ler ou escrever acesso ao seu serviço.
> * Ajuste a capacidade do serviço alterando a atribuição de divisórias e réplicas.
> * Monitorize a utilização dos recursos, em relação aos limites máximos do seu nível de serviço.

Note que *a atualização* não está listada como uma tarefa administrativa. Uma vez que os recursos são atribuídos quando o serviço é prestado, a mudança para um nível diferente requer um novo serviço. Para mais detalhes, consulte Criar um serviço de [Pesquisa Cognitiva Azure.](search-create-service-portal.md)

Pode monitorizar o volume de consultas e outras métricas e utilizar esses insights para ajustar o seu serviço para tempos de resposta mais rápidos. Para mais informações, consulte a [utilização do Monitor e as métricas](search-monitor-usage.md) de consulta e [desempenho e otimização.](search-performance-optimization.md)

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
O fornecimento ou desmantelamento do próprio serviço pode ser feito por um administrador de subscrição do Azure ou coadministrador.

Dentro de um serviço, qualquer pessoa com acesso ao URL de serviço e uma chave de api-key de administração tem acesso de leitura ao serviço. O acesso à leitura de escrita fornece a capacidade de adicionar, excluir ou modificar objetos do servidor, incluindo api-keys, índices, indexadores, fontes de dados, horários e atribuições de papéis implementadas através de [funções definidas pelo RBAC](search-security-rbac.md).

Toda a interação do utilizador com a Pesquisa Cognitiva Azure insere-se num destes modos: acesso de leitura-escrita ao serviço (direitos de administrador) ou acesso apenas a leitura ao serviço (direitos de consulta). Para mais informações, consulte [Gerir as teclas api.](search-security-api-keys.md)

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Registo e informação do sistema
A Pesquisa Cognitiva Azure não expõe ficheiros de registo para um serviço individual, seja através do portal ou das interfaces programáticas. No nível Básico e acima, a Microsoft monitoriza todos os serviços de Pesquisa Cognitiva Azure para 99,9% de disponibilidade por acordos de nível de serviço (SLA). Se o serviço for lento ou o pedido de entrada ficar abaixo dos limiares de SLA, as equipas de suporte revejam os ficheiros de registo disponíveis e resolvam o problema.

Em termos de informações gerais sobre o seu serviço, pode obter informações das seguintes formas:

* No portal, no painel de atendimento, através de notificações, propriedades e mensagens de estado.
* Utilizando o [PowerShell](search-manage-powershell.md) ou a [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/) para obter propriedades de [serviço,](https://docs.microsoft.com/rest/api/searchmanagement/services)ou estado no uso de recursos indexados.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos
No painel de instrumentos, a monitorização de recursos limita-se às informações mostradas no painel de instrumentos de serviço e a algumas métricas que pode obter consultando o serviço. No painel de instrumentos de serviço, na secção utilização, pode determinar rapidamente se os níveis de recursos de partição são adequados para a sua aplicação. Pode fornecer recursos externos, como a monitorização do Azure, se quiser capturar e persistir eventos registados. Para mais informações, consulte [Monitorização da Pesquisa Cognitiva Azure](search-monitor-usage.md).

Utilizando o serviço de pesquisa REST API, pode contar com documentos e índices programáticamente: 

* [Obter Estatísticas de Índices](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Documentos de Contagem](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastres e interrupções de serviço

Embora possamos salvar os seus dados, a Pesquisa Cognitiva Azure não fornece falhas instantâneas do serviço se houver uma falha no nível de cluster ou centro de dados. Se um cluster falhar no centro de dados, a equipa de operações detetará e trabalhará para restaurar o serviço. Você vai experimentar tempo de inatividade durante a restauração do serviço, mas você pode solicitar créditos de serviço para compensar a indisponibilidade de serviço de acordo de nível de [serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessário um serviço contínuo em caso de falhas catastróficas fora do controlo da Microsoft, poderá [fornecer um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de georeplicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que usam [indexadores](search-indexer-overview.md) para povoar e atualizar índices podem lidar com a recuperação de desastres através de indexadores geoespecíficos que alavancam a mesma fonte de dados. Dois serviços em diferentes regiões, cada um com um indexante, poderiam indexar a mesma fonte de dados para alcançar o geo-redundância. Se estiver a indexar a partir de fontes de dados que também são geo-redundantes, esteja ciente de que os indexadores de Pesquisa Cognitiva Azure só podem realizar indexação incremental (atualizações de fusão de novos documentos, modificados ou eliminados) a partir de réplicas primárias. Num evento de failover, certifique-se de reapontar o indexante para a nova réplica primária. 

Se não utilizar os indexadores, utilizará o seu código de aplicação para empurrar objetos e dados para diferentes serviços de pesquisa em paralelo. Para mais informações, consulte [Performance e otimização em Pesquisa Cognitiva Azure](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Como a Pesquisa Cognitiva Azure não é uma solução primária de armazenamento de dados, não fornecemos um mecanismo formal para a cópia de segurança e restauro do self-service. No entanto, pode utilizar o código de amostra **de restauro de cópias** de segurança de índice neste repo de [amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup da sua definição de índice e instantâneo para uma série de ficheiros JSON e, em seguida, usar estes ficheiros para restaurar o índice, se necessário. Esta ferramenta também pode mover índices entre os níveis de serviço.

Caso contrário, o seu código de aplicação utilizado para criar e povoar um índice é a opção de restauro de facto se eliminar um índice por engano. Para reconstruir um índice, eliminaria-o (assumindo que existe), recriaria o índice no serviço e recarregaria recuperando dados da sua loja de dados primária.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Cada serviço de pesquisa começa com um mínimo de uma réplica e uma divisória. Se se inscreveu para um [nível que forneça recursos dedicados,](search-limits-quotas-capacity.md)clique no azulejo **SCALE** no painel de instrumentos de serviço para ajustar o uso de recursos.

Quando adiciona capacidade através de qualquer recurso, o serviço utiliza-os automaticamente. Não são necessárias mais medidas da sua parte, mas há um ligeiro atraso antes de o impacto do novo recurso ser realizado. Pode levar 15 minutos ou mais para fornecer recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
O aumento das consultas por segundo (QPS) ou a obtenção de uma elevada disponibilidade é feito adicionando réplicas. Cada réplica tem uma cópia de um índice, por isso adicionar mais uma réplica traduz-se em mais um índice disponível para o manuseamento de pedidos de consulta de serviço. São necessárias no mínimo 3 réplicas para uma elevada disponibilidade (ver [Planeamento de Capacidade](search-capacity-planning.md) para mais detalhes).

Um serviço de pesquisa com mais réplicas pode carregar pedidos de consulta de saldo sobre um maior número de índices. Dado um nível de volume de consulta, a produção de consulta será mais rápida quando houver mais cópias do índice disponível para atender o pedido. Se estiver a sentir latência de consulta, pode esperar um impacto positivo no desempenho uma vez que as réplicas adicionais estejam online.

Embora a entrada de consulta sacuda à medida que adiciona réplicas, não duplica nem triplica precisamente à medida que adiciona réplicas ao seu serviço. Todas as aplicações de pesquisa estão sujeitas a fatores externos que podem influenciar o desempenho da consulta. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta de consulta.

### <a name="add-partitions"></a>Adicionar divisórias
A maioria das aplicações de serviço tem uma necessidade incorporada de mais réplicas do que divisórias. Para os casos em que é necessária uma contagem de documentos acrescida, pode adicionar divisórias se se inscrever no serviço Standard. O nível básico não prevê divisórias adicionais.

No nível Standard, as divisórias são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefacto de sharding. Um índice é criado em 12 fragmentos, que podem ser armazenados em 1 divisória ou igualmente divididos em 2, 3, 4, 6 ou 12 divisórias (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas
Após períodos de volumes de consultas elevadas, pode utilizar o slider para reduzir as réplicas após a normalização das cargas de consulta de pesquisa (por exemplo, após as vendas de férias terem terminado). Não são necessários mais passos da sua parte. A redução da contagem de réplicas renuncia às máquinas virtuais no centro de dados. As suas operações de consulta e ingestão de dados passarão agora com menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover divisórias
Em contraste com a remoção de réplicas, que não requer esforço extra da sua parte, poderá ter algum trabalho a fazer se estiver a utilizar mais armazenamento do que pode ser reduzido. Por exemplo, se a sua solução estiver a utilizar três divisórias, reduzir o tamanho de uma ou duas divisórias gerará um erro se o novo espaço de armazenamento for inferior ao necessário para hospedar o seu índice. Como seria de esperar, as suas escolhas são apagar índices ou documentos dentro de um índice associado para libertar espaço ou manter a configuração atual.

Não existe um método de deteção que lhe diga quais os fragmentos de índice armazenados em divisórias específicas. Cada partição fornece aproximadamente 25 GB de armazenamento, pelo que terá de reduzir o armazenamento para um tamanho que pode ser acomodado pelo número de divisórias que tem. Se quiser voltar a uma partição, todos os 12 fragmentos terão de caber.

Para ajudar no planeamento futuro, é melhor verificar o armazenamento (usando estatísticas do [Get Index)](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)para ver quanto realmente utilizou. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes
Assim que compreender os conceitos por detrás da administração de serviços, considere usar o [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Recomendamos também a revisão do artigo de [desempenho e otimização.](search-performance-optimization.md)

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



