---
title: Administração de serviços para Azure Search no portal-Azure Search
description: Gerenciar um serviço de Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure, usando o portal do Azure.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2c4b2a03e7e5c818453eaf4ad6881b2caba3b93c
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647667"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração de serviço para Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

O Azure Search é um serviço de pesquisa baseado em nuvem totalmente gerenciado usado para criar uma experiência de pesquisa avançada em aplicativos personalizados. Este artigo aborda as tarefas de administração de serviço que podem ser executadas no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa que você já provisionou. A administração do serviço é leve por design, limitada às seguintes tarefas:

> [!div class="checklist"]
> * Gerencie o acesso às *chaves de API* usadas para acesso de leitura ou gravação ao seu serviço.
> * Ajuste a capacidade do serviço alterando a alocação de partições e réplicas.
> * Monitore o uso de recursos, em relação aos limites máximos de sua camada de serviço.

Observe que a *atualização* não está listada como uma tarefa administrativa. Como os recursos são alocados quando o serviço é provisionado, a mudança para uma camada diferente requer um novo serviço. Para obter detalhes, consulte [criar um serviço de Azure Search](search-create-service-portal.md).

> [!Tip]
> Procurando ajuda sobre como analisar o desempenho de consulta ou o tráfego de pesquisa? Você pode monitorar o volume de consulta, quais termos as pessoas pesquisam e como os resultados da pesquisa bem-sucedidos estão em orientar clientes para documentos específicos no índice. Para obter mais informações, consulte [pesquisar análise de tráfego por Azure Search](search-traffic-analytics.md), monitorar as métricas de [uso e consulta](search-monitor-usage.md)e o [desempenho e a otimização](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
O provisionamento ou encerramento do serviço em si pode ser feito por um administrador ou coadministrador de assinatura do Azure.

Dentro de um serviço, qualquer pessoa com acesso à URL de serviço e uma chave de API de administrador tem acesso de leitura/gravação ao serviço. Acesso de leitura/gravação fornece a capacidade de adicionar, excluir ou modificar objetos de servidor, incluindo chaves de API, índices, indexadores, fontes de dados, agendas e atribuições de função, conforme implementado por meio de [funções definidas pelo RBAC](search-security-rbac.md).

Toda a interação do usuário com Azure Search cai dentro de um destes modos: acesso de leitura/gravação ao serviço (direitos de administrador) ou acesso somente leitura ao serviço (direitos de consulta). Para obter mais informações, consulte [gerenciar as chaves de API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Registro em log e informações do sistema
Azure Search não expõe arquivos de log para um serviço individual por meio do portal ou interfaces programáticas. Na camada básica e acima, a Microsoft monitora todos os serviços de Azure Search por 99,9% de disponibilidade por SLA (contratos de nível de serviço). Se o serviço for lento ou a taxa de transferência de solicitação cair abaixo dos limites de SLA, as equipes de suporte examinarão os arquivos de log disponíveis para eles e resolverão o problema.

Em termos de informações gerais sobre seu serviço, você pode obter informações das seguintes maneiras:

* No portal, no painel de serviço, por meio de notificações, propriedades e mensagens de status.
* Usando o [PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter propriedades de serviço](https://docs.microsoft.com/rest/api/searchmanagement/services)ou status no uso de recursos de índice.
* Por meio da [análise de tráfego de pesquisa](search-traffic-analytics.md), conforme observado anteriormente.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorar o uso de recursos
No painel, o monitoramento de recursos é limitado às informações mostradas no painel de serviço e algumas métricas que você pode obter consultando o serviço. No painel de serviço, na seção uso, você pode determinar rapidamente se os níveis de recursos de partição são adequados para seu aplicativo. Você pode provisionar recursos externos, como o monitoramento do Azure, se desejar capturar e persistir eventos registrados. Para obter mais informações, consulte [monitoring Azure Search](search-monitor-usage.md).

Usando a API REST do Serviço de Pesquisa, você pode obter uma contagem de documentos e índices programaticamente: 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contagem de documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Interrupções de serviço e recuperação de desastre

Embora possamos recuperar seus dados, Azure Search não fornece um failover instantâneo do serviço se houver uma interrupção no nível do cluster ou do data center. Se um cluster falhar na data center, a equipe de operações irá detectar e trabalhar para restaurar o serviço. Você passará por tempo de inatividade durante a restauração do serviço, mas poderá solicitar créditos de serviço para compensar a indisponibilidade do serviço de acordo com o [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se o serviço contínuo for necessário no caso de falhas catastróficas fora do controle da Microsoft, você poderá provisionar [um serviço adicional](search-create-service-portal.md) em uma região diferente e implementar uma estratégia de replicação geográfica para garantir que os índices sejam totalmente redundantes em todos os serviços.

Os clientes que [](search-indexer-overview.md) usam indexadores para popular e atualizar índices podem lidar com a recuperação de desastre por meio de indexadores específicos de Geografia aproveitando a mesma fonte de dados. Dois serviços em regiões diferentes, cada um executando um indexador, podem indexar a mesma fonte de dados para obter redundância geográfica. Se você estiver indexando de fontes de dados que também são com redundância geográfica, lembre-se de que Azure Search indexadores só podem executar a indexação incremental de réplicas primárias. Em um evento de failover, certifique-se de apontar novamente o indexador para a nova réplica primária. 

Se você não usar indexadores, você usaria o código do aplicativo para enviar objetos e dados por push a diferentes serviços de pesquisa em paralelo. Para obter mais informações, consulte [desempenho e otimização no Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Como Azure Search não é uma solução de armazenamento de dados primária, não fornecemos um mecanismo formal para backup e restauração de autoatendimento. O código do aplicativo usado para criar e popular um índice é a opção de restauração de fato se você excluir um índice por engano. 

Para recriar um índice, você o excluirá (supondo que ele exista), recriará o índice no serviço e recarregará recuperando dados do seu armazenamento de dados primário.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Cada serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se você se inscreveu para uma [camada que fornece recursos dedicados](search-limits-quotas-capacity.md), clique no bloco **escala** no painel de serviço para ajustar o uso do recurso.

Quando você adiciona capacidade por meio de qualquer recurso, o serviço os usa automaticamente. Nenhuma ação adicional é necessária em sua parte, mas há um pequeno atraso antes que o impacto do novo recurso seja realizado. Pode levar 15 minutos ou mais para provisionar recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
O aumento de consultas por segundo (QPS) ou a obtenção de alta disponibilidade é feito adicionando réplicas. Cada réplica tem uma cópia de um índice, portanto, adicionar mais uma réplica se traduz em mais um índice disponível para lidar com solicitações de consulta de serviço. Um mínimo de 3 réplicas é necessário para alta disponibilidade (consulte [planejamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa com mais réplicas pode balancear a carga de solicitações de consulta em um número maior de índices. Dado um nível de volume de consulta, a taxa de transferência da consulta será mais rápida quando houver mais cópias do índice disponíveis para atender à solicitação. Se você estiver tendo latência de consulta, poderá esperar um impacto positivo no desempenho quando as réplicas adicionais estiverem online.

Embora a taxa de transferência de consulta fique ativa conforme você adiciona réplicas, ela não é precisamente dupla ou tripla à medida que você adiciona réplicas ao seu serviço. Todos os aplicativos de pesquisa estão sujeitos a fatores externos que podem executar o ping no desempenho da consulta. As consultas complexas e a latência de rede são dois fatores que contribuem para variações nos tempos de resposta da consulta.

### <a name="add-partitions"></a>Adicionar partições
A maioria dos aplicativos de serviço tem uma necessidade interna de mais réplicas em vez de partições. Para os casos em que uma contagem de documentos aumentada é necessária, você pode adicionar partições se tiver se inscrito no serviço padrão. A camada básica não fornece partições adicionais.

Na camada Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefato de fragmentação. Um índice é criado em 12 fragmentos, que podem ser armazenados em 1 partição ou igualmente divididos em 2, 3, 4, 6 ou 12 partições (um fragmento por partição).

### <a name="remove-replicas"></a>Remover réplicas
Após períodos de grandes volumes de consulta, você pode usar o controle deslizante para reduzir as réplicas depois que as cargas de consulta de pesquisa tiverem sido normalizadas (por exemplo, depois que as vendas de feriado estiverem acima). Não há mais etapas necessárias para sua parte. A redução da contagem de réplicas abandona as máquinas virtuais no data center. Suas operações de consulta e ingestão de dados agora serão executadas em menos VMs do que antes. O requisito mínimo é uma réplica.

### <a name="remove-partitions"></a>Remover partições
Em contraste com a remoção de réplicas, o que não exige esforço adicional de sua parte, talvez você tenha algum trabalho para fazer se estiver usando mais armazenamento do que pode ser reduzido. Por exemplo, se sua solução estiver usando três partições, o downsizing para uma ou duas partições gerará um erro se o novo espaço de armazenamento for menor do que o necessário para hospedar o índice. Como você pode esperar, suas opções são excluir índices ou documentos dentro de um índice associado para liberar espaço ou manter a configuração atual.

Não há nenhum método de detecção que indique quais fragmentos de índice são armazenados em partições específicas. Cada partição fornece aproximadamente 25 GB de armazenamento, portanto, será necessário reduzir o armazenamento para um tamanho que possa ser acomodado pelo número de partições que você tem. Se você quiser reverter para uma partição, todos os 12 fragmentos deverão se ajustar.

Para ajudar no futuro planejamento, talvez você queira verificar o armazenamento (usando [obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver quanto você realmente usou. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Práticas recomendadas em escala e implantação
Este vídeo de 30 minutos examina as práticas recomendadas para cenários de implantação avançada, incluindo cargas de trabalho distribuídas geograficamente. Você também pode ver o [desempenho e a otimização em Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes
Depois de entender os conceitos por trás da administração do serviço, considere usar o [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Também recomendamos a revisão do [artigo desempenho e otimização](search-performance-optimization.md).

Outra recomendação é assistir ao vídeo anotado na seção anterior. Ele fornece uma cobertura mais profunda das técnicas mencionadas nesta seção.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



