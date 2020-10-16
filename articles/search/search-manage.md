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
ms.date: 06/24/2020
ms.openlocfilehash: 84ddc4b427f6dc168c044f34b41e81e3b0ff19e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935046"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administração de serviços para Azure Cognitive Search no portal Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [API REST](/rest/api/searchmanagement/)
> * [SDK do .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Pitão](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A Azure Cognitive Search é um serviço de pesquisa totalmente gerido e baseado na nuvem usado para construir uma rica experiência de pesquisa em apps personalizadas. Este artigo cobre as tarefas de administração de serviços que pode desempenhar no [portal Azure](https://portal.azure.com) para um serviço de pesquisa que já forjado. A administração de serviços é leve por design, limitada às seguintes tarefas:

* Verifique o armazenamento utilizando o link de **utilização de página** média.
* Verifique os volumes de consulta e a latência utilizando o link de **monitorização** de página média e se os pedidos foram acelerados.
* Gerencie o acesso utilizando a página **Keys** à esquerda.
* Ajuste a capacidade utilizando a página **Escala** para a esquerda.

As mesmas tarefas executadas no portal também podem ser tratadas programáticamente através das [APIs de Gestão](/rest/api/searchmanagement/) e [módulo Az.Search PowerShell](search-manage-powershell.md). As tarefas administrativas estão totalmente representadas em interfaces portais e programáticas. Não existe uma tarefa administrativa específica que esteja disponível apenas numa modalidade.

A Azure Cognitive Search aproveita outros serviços Azure para uma monitorização e gestão mais profundas. Por si só, os únicos dados armazenados com um serviço de pesquisa são o conteúdo (índices, indexantes e definições de fonte de dados, e outros objetos). As métricas reportadas às páginas do portal são retiradas de troncos internos num ciclo de 30 dias. Para retenção de registos controlada pelo utilizador e eventos adicionais, necessitará do [Azure Monitor](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Propriedades de serviço fixo

Vários aspetos de um serviço de pesquisa são determinados quando o serviço é prestado e não podem ser alterados mais tarde:

* Nome de serviço (não é possível renomear um serviço)
* Localização do serviço (não é possível deslocar um serviço intacto para outra região)
* Contas máximas de replicação e partição (determinadas pelo nível, Básico ou Standard)

Se começou com o Basic com o seu máximo de uma partição, e agora precisa de mais divisórias, terá de [criar um novo serviço](search-create-service-portal.md) num nível mais elevado e recriar o seu conteúdo no novo serviço. 

## <a name="administrator-rights"></a>Direitos de administrador

O fornecimento ou desmantelamento do próprio serviço pode ser feito por um administrador ou coadministrador de subscrição da Azure.

No que diz respeito ao acesso ao ponto final, qualquer pessoa com acesso ao URL de serviço e a uma chave API tem acesso aos conteúdos. Para obter mais informações sobre as teclas, consulte [Gerir as teclas api](search-security-api-keys.md).

* O acesso apenas à leitura do serviço é direitos de consulta, normalmente concedidos a uma aplicação de cliente, dando-lhe o URL e uma chave api de consulta.
* O acesso à escrita de leitura fornece a capacidade de adicionar, eliminar ou modificar objetos do servidor, incluindo chaves api, índices, indexadores, fontes de dados e horários. O acesso à leitura-escrita é concedido através da indicação do URL, uma chave API de administração.

Os direitos ao aparelho de prestação de serviços são concedidos através de atribuições de funções. [O Azure role-based access control (Azure RBAC)](../role-based-access-control/overview.md) é um sistema de autorização construído no [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o fornecimento de recursos Azure. 

No contexto da Azure Cognitive Search, as atribuições de [funções da Azure](search-security-rbac.md) determinarão quem pode executar tarefas, independentemente de estarem a utilizar o [portal,](search-manage.md) [o PowerShell,](search-manage-powershell.md)ou as [APIs de Rest de Gestão:](/rest/api/searchmanagement/search-howto-management-rest-api)

* Criar ou apagar um serviço
* Dimensione o serviço
* Eliminar ou regenerar chaves API
* Ativar a registo de diagnóstico (criar serviços)
* Ativar a análise de tráfego (criar serviços)

> [!TIP]
> Utilizando mecanismos azures, pode bloquear uma subscrição ou recurso para evitar a eliminação acidental ou não não autorizada do seu serviço de pesquisa por utilizadores com direitos de administração. Para obter mais informações, consulte [os recursos de bloqueio para evitar a eliminação inesperada.](../azure-resource-manager/management/lock-resources.md)

## <a name="logging-and-system-information"></a>Informação de registo e sistema

No nível básico e acima, a Microsoft monitoriza todos os serviços de Pesquisa Cognitiva Azure para uma disponibilidade de 99,9% por acordos de nível de serviço (SLA). Se o serviço for lento ou o pedido de saída ficar abaixo dos limiares de SLA, as equipas de suporte analisam os ficheiros de registo disponíveis e resolvem o problema.

A Azure Cognitive Search aproveita [o Azure Monitor](../azure-monitor/index.yml) para recolher e armazenar atividade de indexação e consulta. Um serviço de pesquisa por si só armazena apenas o seu conteúdo (índices, definições indexantes, definições de fonte de dados, definições de skillset, mapas de sinónimo). A informação de caching e registrado é armazenada fora de serviço, muitas vezes numa conta de Armazenamento Azure. Para obter mais informações sobre a indexação de registos e consultas, consulte [recolher e analisar dados de registo.](search-monitor-logs.md)

Em termos de informação geral sobre o seu serviço, utilizando apenas as instalações incorporadas na própria Pesquisa Cognitiva do Azure, pode obter informações das seguintes formas:

* Utilização da página **de visão geral** do serviço, através de notificações, propriedades e mensagens de estado.
* Utilizar [a PowerShell](search-manage-powershell.md) ou a [API Management REST](/rest/api/searchmanagement/) para obter propriedades de [serviço.](/rest/api/searchmanagement/services) Não há novas informações ou operações fornecidas na camada programática. As interfaces existem para que possa escrever scripts.

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos

No painel de instrumentos, a monitorização dos recursos limita-se às informações apresentadas no painel de serviço e a algumas métricas que pode obter consultando o serviço. No painel de instrumentos de serviço, na secção Utilização, pode determinar rapidamente se os níveis de recursos de partição são adequados para a sua aplicação. Pode obter recursos externos, como a monitorização do Azure, se quiser capturar e persistir em eventos registados. Para obter mais informações, consulte [Monitoring Azure Cognitive Search](search-monitor-usage.md).

Utilizando o serviço de pesquisa REST API, pode obter uma contagem de documentos e índices programáticamente: 

* [Obter Estatísticas de Índice](/rest/api/searchservice/Get-Index-Statistics)
* [Documentos de Contagem](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Recuperação de desastres e interrupções de serviço

Embora possamos resgatar os seus dados, a Azure Cognitive Search não fornece falha instantânea do serviço se houver uma falha no nível do cluster ou do centro de dados. Se um cluster falhar no centro de dados, a equipa de operações detetará e trabalhará para restaurar o serviço. Você vai experimentar tempo de paragem durante a restauração do serviço, mas você pode solicitar créditos de serviço para compensar a indisponibilidade de serviço de acordo com o [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessário um serviço contínuo em caso de falhas catastróficas fora do controlo da Microsoft, poderá [providenciar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de geo-replicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que usam [indexantes](search-indexer-overview.md) para preencher e atualizar índices podem lidar com a recuperação de desastres através de indexadores geo-específicos alavancando a mesma fonte de dados. Dois serviços em diferentes regiões, cada um com um indexante, poderiam indexar a mesma fonte de dados para alcançar a geo-redundância. Se estiver a indexar a partir de fontes de dados que também são geo-redundantes, esteja ciente de que os indexantes de Pesquisa Cognitiva do Azure só podem realizar indexação incremental (fundindo atualizações de documentos novos, modificados ou eliminados) a partir de réplicas primárias. Num evento de failover, certifique-se de voltar a apontar o indexante para a nova réplica primária. 

Se não utilizar indexadores, utilizará o seu código de aplicação para empurrar objetos e dados para diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [Performance e otimização na Pesquisa Cognitiva Azure.](search-performance-optimization.md)

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Como a Azure Cognitive Search não é uma solução primária de armazenamento de dados, não fornecemos um mecanismo formal de backup e restauro de autosserviço. No entanto, pode utilizar o código de amostra **de backup-backup** neste [Azure Cognitive Search .NET sample repo](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup da sua definição de índice e instantâneo para uma série de ficheiros JSON, e depois usar estes ficheiros para restaurar o índice, se necessário. Esta ferramenta também pode mover índices entre os níveis de serviço.

Caso contrário, o seu código de aplicação utilizado para criar e povoar um índice é a opção de restauro de facto se eliminar um índice por engano. Para reconstruir um índice, apagar-se-ia (assumindo que existe), recriaria o índice no serviço e recarregava-se recuperando dados da sua loja de dados primário.

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente

Cada serviço de pesquisa começa com um mínimo de uma réplica e uma divisória. Se se inscreveu para um [nível que suporta mais capacidade,](search-limits-quotas-capacity.md)clique em **Escala** no painel de navegação esquerdo para ajustar a utilização do recurso.

Quando adiciona capacidade através de qualquer recurso, o serviço utiliza-os automaticamente. Não são necessárias mais medidas da sua parte, mas há um ligeiro atraso antes do impacto do novo recurso ser realizado. Pode levar 15 minutos ou mais para a disponibilização de recursos adicionais.

### <a name="add-replicas"></a>Adicionar réplicas

Aumentar as consultas por segundo (QPS) ou alcançar uma elevada disponibilidade é feito adicionando réplicas. Cada réplica tem uma cópia de um índice, pelo que adicionar mais uma réplica traduz-se em mais um índice disponível para lidar com pedidos de consulta de serviço. São necessárias no mínimo 3 réplicas para uma elevada disponibilidade (ver [Ajuste a capacidade](search-capacity-planning.md) para detalhes).

Um serviço de pesquisa com mais réplicas pode carregar pedidos de consulta de equilíbrio sobre um maior número de índices. Dado o nível de volume de consulta, a produção de consulta será mais rápida quando houver mais cópias do índice disponível para o serviço do pedido. Se estiver a sentir latência de consulta, pode esperar um impacto positivo no desempenho uma vez que as réplicas adicionais estejam online.

Embora a produção de consulta suba à medida que adiciona réplicas, não precisamente o dobro ou o triplo à medida que adiciona réplicas ao seu serviço. Todas as aplicações de pesquisa estão sujeitas a fatores externos que podem influenciar o desempenho da consulta. Consultas complexas e latência da rede são dois fatores que contribuem para variações nos tempos de resposta da consulta.

### <a name="add-partitions"></a>Adicionar divisórias

É mais comum adicionar réplicas, mas quando o armazenamento é limitado, pode adicionar divisórias para obter mais capacidade. O nível a que adquirou o serviço determina se as divisórias podem ser adicionadas. O nível básico está bloqueado numa partição. Os níveis padrão e acima suportam divisórias adicionais.

As divisórias são adicionadas em divisores de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefacto de cacos. Um índice é criado em 12 fragmentos, que podem ser armazenados em 1 divisória ou igualmente divididos em 2, 3, 4, 6 ou 12 divisórias (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas

Após períodos de elevados volumes de consultas, pode utilizar o slider para reduzir as réplicas após a normalização das cargas de consulta de pesquisa (por exemplo, após o fim das vendas de férias). Não são necessários mais passos da sua parte. A redução da contagem de réplicas renuncia às máquinas virtuais no centro de dados. As suas operações de consulta e ingestão de dados passarão a funcionar com menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover divisórias

Em contraste com a remoção de réplicas, que não requer nenhum esforço extra da sua parte, você pode ter algum trabalho a fazer se estiver usando mais armazenamento do que pode ser reduzido. Por exemplo, se a sua solução estiver a utilizar três divisórias, a redução para uma ou duas divisórias gerará um erro se o novo espaço de armazenamento for inferior ao necessário para hospedar o seu índice. Como seria de esperar, as suas escolhas são eliminar índices ou documentos dentro de um índice associado para libertar espaço ou manter a configuração atual.

Não existe um método de deteção que lhe diga quais os fragmentos de índice armazenados em divisórias específicas. Cada divisória fornece aproximadamente 25 GB de armazenamento, por isso terá de reduzir o armazenamento para um tamanho que pode ser acomodado pelo número de divisórias que tem. Se quiser voltar a uma partição, todos os 12 fragmentos terão de caber.

Para ajudar no planeamento futuro, pode querer verificar o armazenamento (usando [as estatísticas do Índice Get](/rest/api/searchservice/Get-Index-Statistics)) para ver quanto realmente utilizou. 

## <a name="next-steps"></a>Passos seguintes

* Automatize com [PowerShell](search-manage-powershell.md)

* Rever técnicas [de desempenho e otimização](search-performance-optimization.md)

* Rever [funcionalidades de segurança](search-security-overview.md) para proteger conteúdos e operações

* Permitir [a verificação de trabalhos](search-monitor-logs.md) de consulta e indexação de consultas