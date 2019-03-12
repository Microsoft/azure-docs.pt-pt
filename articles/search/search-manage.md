---
title: Administração de serviço para o Azure Search no portal - Azure Search
description: Gerir um serviço de Azure Search, um serviço de pesquisa de nuvem alojada no Microsoft Azure, com o portal do Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775599"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração de serviço para o Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

O Azure Search é um serviço de pesquisa totalmente gerido e baseado na nuvem utilizado para a criação de uma experiência de pesquisa sofisticada em aplicações personalizadas. Este artigo aborda as tarefas de administração de serviço que pode efetuar no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa que já provisionou. Administração de serviços é leve por design, limitado para as seguintes tarefas:

> [!div class="checklist"]
> * Gerir o acesso para o *chaves de api* utilizado para leitura ou acesso de escrita ao seu serviço.
> * Ajuste a capacidade de serviço ao alterar a alocação de réplicas e partições.
> * Monitorizar a utilização de recursos, em relação ao limites máximos do escalão de serviço.

Tenha em atenção que *atualizar* não estiver listado como uma tarefa administrativa. Uma vez que os recursos são alocados quando o serviço é aprovisionado, mover para um escalão diferente requer um novo serviço. Para obter detalhes, consulte [criar um serviço Azure Search](search-create-service-portal.md).

> [!Tip]
> Procurando ajuda sobre como analisar o desempenho de tráfego ou consulta de pesquisa? Pode monitorar o volume de consulta, o que as pessoas termos de pesquisa para e pesquisa com o êxito dos resultados estão no orientador aos clientes documentos específicos no seu índice. Para obter mais informações, consulte [procurar análises de tráfego para o Azure Search](search-traffic-analytics.md), [monitorizar métricas de utilização e de consulta](search-monitor-usage.md), e [desempenho e otimização](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
Aprovisionamento ou desativar o serviço em si pode ser feito por um administrador de subscrição do Azure ou o coadministrador.

Dentro de um serviço, qualquer pessoa com acesso para o URL do serviço e uma chave de api de administrador tem acesso de leitura / escrita ao serviço. Acesso de leitura-escrita fornece a capacidade de adicionar, eliminar ou modificar objetos de servidor, incluindo as chaves de api, índices, indexadores, origens de dados, agendas e atribuições de função, conforme implementado por meio [funções definidas pelo RBAC](search-security-rbac.md).

Todas as interações de utilizador com o Azure Search está dentro de um desses modos: acesso de leitura / escrita para o serviço (direitos de administrador), ou acesso só de leitura para o serviço (direitos de consulta). Para obter mais informações, consulte [gerir as chaves de api](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Informações de registo e sistema
O Azure Search não expõe os ficheiros de registo para um serviço individual através do portal ou a interfaces de programação. No escalão básico e acima, a Microsoft monitoriza todos os serviços do Azure Search para 99,9% de disponibilidade por contratos de nível de serviço (SLA). Se o serviço está lento ou débito de pedidos cai abaixo de limiares de SLA, equipes de suporte rever os ficheiros de registo disponíveis e resolver o problema.

Em termos de informações gerais sobre o seu serviço, pode obter as informações das seguintes formas:

* No portal, no dashboard do serviço, através de notificações, propriedades e as mensagens de estado.
* Usando [PowerShell](search-manage-powershell.md) ou o [API de REST de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter as propriedades do serviço](https://docs.microsoft.com/rest/api/searchmanagement/services), ou o estado na utilização de recursos do índice.
* Via [a análise de tráfego de pesquisa](search-traffic-analytics.md), conforme indicado anteriormente.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos
No dashboard, monitorização de recursos está limitado às informações mostradas no dashboard do serviço e algumas métricas que pode obter ao consultar o serviço. No dashboard do serviço, na secção utilização, é possível determinar rapidamente se os níveis de recursos de partição são adequados para a sua aplicação. Pode aprovisionar recursos externos, como a monitorização do Azure, se quiser capturar e manter os eventos registrados. Para obter mais informações, consulte [monitorização do Azure Search](search-monitor-usage.md).

Com a API de REST do serviço de pesquisa, pode obter uma contagem de documentos e índices por meio de programação: 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contagem de documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Falhas de serviço e de recuperação após desastre

Embora nós pode recuperá-los seus dados, do Azure Search não fornece ativação pós-falha instantânea do serviço se houver uma falha no nível de centro de dados ou de cluster. Se falhar um cluster no Centro de dados, a equipe de operações detectará e trabalhar para restaurar o serviço. Terá um período de indisponibilidade durante a restauração do serviço, mas pode solicitar os créditos de serviço para compensar indisponibilidade de serviço pela [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se o serviço contínuo é necessário em caso de falhas catastróficas fora do controlo da Microsoft, poderia [aprovisionar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de replicação geográfica para garantir que os índices são totalmente redundante em todos os serviços.

Os clientes que utilizam [indexadores](search-indexer-overview.md) preencher e atualizar índices pode manipular a recuperação após desastre através de indexadores geográfica específica tirar partido da mesma origem de dados. Dois serviços em regiões diferentes, cada execução de um indexador podem indexar a mesma origem de dados para obter redundância geográfica. Se estiver indexando de origens de dados que também estão geograficamente redundante, tenha em atenção que indexadores do Azure Search só podem executar a indexação incremental de réplicas primárias. Num evento de ativação pós-falha, certifique-se de que o indexador de ponto novamente para a nova réplica primária. 

Se não utilizar indexadores, tem de utilizar dados para os serviços de pesquisa diferentes e de código da aplicação para objetos de push em paralelo. Para obter mais informações, consulte [desempenho e otimização no Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Como o Azure Search não é uma solução de armazenamento de dados principal, não fornecemos um mecanismo formal self-service a cópia de segurança e restauro. O código da aplicação utilizado para criar e preencher um índice é a opção de restauro de fato, se eliminar um índice por engano. 

Para reconstruir um índice, seria eliminá-lo (supondo que exista), recrie o índice no serviço e recarregar ao obter dados a partir do seu arquivo de dados primário.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Todos os serviços de pesquisa começa com um mínimo de uma réplica e de uma partição. Se se inscreveu para obter um [escalão que fornece recursos dedicados](search-limits-quotas-capacity.md), clique nas **DIMENSIONAMENTO** mosaico no dashboard do serviço para ajustar a utilização de recursos.

Ao adicionar capacidade por meio de qualquer um dos recursos, o serviço utiliza automaticamente. Não é necessária nenhuma ação adicional sua parte, mas há um pequeno atraso antes do impacto do novo recurso é realizado. Pode demorar 15 minutos ou mais para aprovisionar os recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
Aumentar as consultas por segundo (QPS) ou a obtenção da elevada disponibilidade é feito pela adição de réplicas. Cada réplica tem uma cópia de um índice, para que adicionar uma réplica mais traduz-se um índice mais disponível para processar pedidos de consulta do serviço. Um mínimo de 3 réplicas são necessários para elevada disponibilidade (veja [planeamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa com réplicas mais a carga de pedidos de consulta de saldo ao longo de um maior número de índices. Devido um nível de volume de consultas, débito de consulta vai ser mais rápido quando existe mais cópias do índice disponível para responder ao pedido. Se estiver a ter latência da consulta, pode esperar um impacto positivo no desempenho, uma vez que as réplicas adicionais estão online.

Embora o débito de consulta sobe à medida que adiciona as réplicas, não exatamente duas vezes ou triplicar como adicionar réplicas ao seu serviço. Todas as aplicações de pesquisa estão sujeitos a fatores externos que afetará o desempenho de consulta. Consultas complexas e latência de rede são dois fatores que contribuem para variações em tempos de resposta da consulta.

### <a name="add-partitions"></a>Adicionar partições
A maioria dos aplicativos de serviço têm uma necessidade interna para obter mais réplicas em vez de partições. Para os casos em que é necessária uma contagem de documentos maior, pode adicionar partições, se se inscreveu no serviço Standard. Escalão básico não fornece para partições adicionais.

No escalão Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefato da fragmentação. Um índice é criado em 12 partições horizontais, que podem todos ser armazenadas na 1 partição ou divididas igualmente no 2, 3, 4, 6 ou 12 partições (uma partição por partição).

### <a name="remove-replicas"></a>Remover as réplicas
Depois de períodos de volumes de consulta elevado, pode utilizar o controlo de deslize para reduzir as réplicas após tem normalizados de cargas de consulta de pesquisa (por exemplo, depois de vendas de Natal são sobre). Não há nenhuma outra etapa necessária da sua parte. Reduzir a contagem de réplica ceda máquinas virtuais no Centro de dados. As operações de ingestão de dados e de consultas agora serão executados nas VMs menos do que antes. O requisito mínimo é uma das réplicas.

### <a name="remove-partitions"></a>Remover partições
Ao contrário de remover as réplicas, que requer um esforço extra de sua parte, poderá ser necessário algum trabalho para o fazer se estiver a utilizar o armazenamento de mais do que pode ser reduzido. Por exemplo, se sua solução está a utilizar três partições, downsizing para uma ou duas partições irá gerar um erro se o novo espaço de armazenamento é menor do que o exigido para alojar o seu índice. Como pode esperar, suas opções são eliminar índices ou documentos dentro de um índice associado para libertar espaço ou mantenha a configuração atual.

Não existe nenhum método de deteção que indica quais as partições horizontais de índice são armazenadas em partições específicas. Cada partição fornece cerca de 25 GB no armazenamento, portanto, será necessário reduzir o armazenamento para um tamanho que pode ser atendido pelo número de partições existentes. Se quiser reverter para uma partição, todas as partições horizontais de 12 precisará para se ajustar.

Para ajudar a planear futuros, convém verificar armazenamento (usando [obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver o quanto realmente usada. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Melhores práticas em escala e a implantação
Este vídeo de 30 minutos analisa as práticas recomendadas para cenários de implementação avançada, incluindo cargas de trabalho distribuída geograficamente. Também pode ver [desempenho e otimização no Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos Seguintes
Assim que compreender os conceitos por trás de administração do serviço, considere a utilização [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Também recomendamos que reveja os [artigo de desempenho e otimização](search-performance-optimization.md).

Outra recomendação é ver o vídeo indicado na secção anterior. Ele fornece uma cobertura mais aprofundada das técnicas mencionados nesta seção.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



