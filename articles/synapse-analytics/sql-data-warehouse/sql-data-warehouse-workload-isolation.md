---
title: Isolamento de cargas de trabalho
description: Orientação para definir o isolamento da carga de trabalho com grupos de carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c30429653c024c669d273c45d12236afa8cdbb83
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591510"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics o isolamento do grupo de trabalho

Este artigo explica como os grupos de carga de trabalho podem ser usados para configurar o isolamento da carga de trabalho, conter recursos e aplicar regras de tempo de execução de consulta.

## <a name="workload-groups"></a>Grupos de carga de trabalho

Os grupos de carga de trabalho são recipientes para um conjunto de pedidos e são a base para a forma como a gestão da carga de trabalho, incluindo o isolamento da carga de trabalho, é configurada num sistema.  Os grupos de carga de trabalho são criados utilizando a sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Uma configuração simples de gestão da carga de trabalho pode gerir cargas de dados e consultas de utilizador.  Por exemplo, um grupo de carga de trabalho nomeado `wgDataLoads` definirá aspetos de carga de trabalho para os dados que estão a ser carregados no sistema. Além disso, um grupo de carga de trabalho nomeado `wgUserQueries` definirá aspetos de carga de trabalho para os utilizadores que executam consultas para ler dados do sistema.

As seguintes secções irão destacar como os grupos de carga de trabalho fornecem a capacidade de definir isolamento, contenção, solicitar definição de recursos e aderir às regras de execução.

## <a name="workload-isolation"></a>Isolamento de cargas de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de carga de trabalho.  O isolamento da carga de trabalho é alcançado configurando o parâmetro MIN_PERCENTAGE_RESOURCE para maior do que zero na sintaxe create [workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Para cargas de trabalho contínuas de execução que precisam aderir a SLAs apertados, o isolamento garante que os recursos estão sempre disponíveis para o grupo de carga de trabalho.

Configurar o isolamento da carga de trabalho define implicitamente um nível garantido de conmoeda. Por exemplo, um grupo de carga de trabalho com um `MIN_PERCENTAGE_RESOURCE` conjunto de 30% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` definido para 2% é garantido 15 conmoeda.  O nível de moeda é garantido porque 15-2% de slots de recursos são reservados no grupo de carga de trabalho em todos os momentos (independentemente de como `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` está configurado).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` for maior do que e for maior do que os recursos `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `CAP_PERCENTAGE_RESOURCE` `MIN_PERCENTAGE_RESOURCE` adicionais são adicionados por pedido.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` forem iguais e `CAP_PERCENTAGE_RESOURCE` forem maiores do `MIN_PERCENTAGE_RESOURCE` que, é possível uma moeda adicional.  Considere o método abaixo para determinar a moeda garantida:

[Concurrency Garantida] = [ `MIN_PERCENTAGE_RESOURCE` [ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Existem valores mínimos de nível de serviço viáveis para min_percentage_resource.  Para mais informações, consulte [Valores Efetivos](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values) para mais informações.

Na ausência de isolamento de carga de trabalho, os pedidos operam no [conjunto partilhado](#shared-pool-resources) de recursos.  O acesso aos recursos na piscina partilhada não está garantido e é atribuído numa base [de importância.](sql-data-warehouse-workload-importance.md)

A configuração do isolamento da carga de trabalho deve ser feita com cautela, uma vez que os recursos são atribuídos ao grupo de carga de trabalho, mesmo que não existam pedidos ativos no grupo de carga de trabalho. O isolamento configurante excessivo pode levar a uma utilização global reduzida do sistema.

Os utilizadores devem evitar uma solução de gestão da carga de trabalho que consuma 100% de isolamento da carga de trabalho: o isolamento 100% é alcançado quando a soma de min_percentage_resource configurada em todos os grupos de carga de trabalho equivale a 100%.  Este tipo de configuração é excessivamente restritiva e rígida, deixando pouco espaço para pedidos de recursos que são acidentalmente mal classificados. Existe uma disposição que permite a execução de um pedido de execução de grupos de carga de trabalho não configurados para isolamento. Os recursos atribuídos a este pedido aparecerão como um zero nos Sistemas DMVs e pedirão emprestado um pequeno nível de subvenção de recursos a partir de recursos reservados do sistema.

> [!NOTE]
> Para garantir uma utilização ótima dos recursos, considere uma solução de gestão da carga de trabalho que aproveite algum isolamento para garantir que os SLAs são cumpridos e misturados com recursos partilhados que são acedidos com base na importância da [carga de trabalho.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Contenção de carga de trabalho

A contenção da carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção da carga de trabalho é conseguida configurando o parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Considere o cenário em que os utilizadores precisam de ler o acesso ao sistema para que possam executar uma análise do que se através de consultas ad-hoc.  Este tipo de pedidos pode ter um impacto negativo em outras cargas de trabalho que estão a ser recorridas no sistema.  Configurar a contenção garante que a quantidade de recursos é limitada.

Configurar a contenção da carga de trabalho define implicitamente um nível máximo de conmoeda.  Com um CAP_PERCENTAGE_RESOURCE fixado para 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido para 1%, até um nível de 60-concurrency é permitido para o grupo de carga de trabalho.  Considere o método abaixo indicado para determinar a moeda máxima:

[Max Concurrency] = [ `CAP_PERCENTAGE_RESOURCE` ] / `REQUEST_MIN_RESOURCE_GRANT_PERCENT` [ ]

> [!NOTE]
> Os CAP_PERCENTAGE_RESOURCE efetivos de um grupo de carga de trabalho não chegarão a 100% quando forem criados grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE a um nível superior a zero.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para valores eficazes do tempo de funcionano.

## <a name="resources-per-request-definition"></a>Recursos por definição de pedido

Os grupos de carga de trabalho fornecem um mecanismo para definir o min e a quantidade máxima de recursos que são atribuídos por pedido com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Os recursos neste caso são cpu e memória.  A configuração destes valores dita quantos recursos e que nível de moeda pode ser alcançado no sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que não se aplica ao mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como escolher uma classe de recursos, configurar REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor para os recursos utilizados por um pedido.  O montante dos recursos indicados pelo valor definido é garantido para a atribuição ao pedido antes de iniciar a execução.  Para os clientes que migram de classes de recursos para grupos de carga de trabalho, considere seguir o artigo [Como mapear](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) das classes de recursos para grupos de carga de trabalho como um ponto de partida.

Configurar REQUEST_MAX_RESOURCE_GRANT_PERCENT a um valor superior ao REQUEST_MIN_RESOURCE_GRANT_PERCENT permite ao sistema alocar mais recursos por pedido.  Ao agendar um pedido, o sistema determina a atribuição real de recursos ao pedido, que é entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade de recursos em pool partilhado e carga atual no sistema.  Os recursos devem existir no [conjunto partilhado](#shared-pool-resources) de recursos quando a consulta está agendada.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT têm valores efetivos que dependem dos valores MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE eficazes.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para valores eficazes do tempo de funcionano.

## <a name="execution-rules"></a>Regras de Execução

Em sistemas de reporte ad-hoc, os clientes podem executar acidentalmente consultas em fuga que impactam severamente a produtividade de outros.  Os administradores do sistema são forçados a passar o tempo a matar consultas em fuga para libertar recursos do sistema.  Os grupos de carga de trabalho oferecem a capacidade de configurar uma regra de tempo limite de execução de consulta para cancelar consultas que excederam o valor especificado.  A regra é configurada através da definição do `QUERY_EXECUTION_TIMEOUT_SEC` parâmetro na sintaxe create [workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Recursos de piscina compartilhados

Os recursos partilhados da piscina são os recursos não configurados para o isolamento.  Grupos de carga de trabalho com um MIN_PERCENTAGE_RESOURCE definido para zero recursos de alavancagem na piscina partilhada para executar pedidos.  Os grupos de trabalho com CAP_PERCENTAGE_RESOURCE maiores do que MIN_PERCENTAGE_RESOURCE também utilizaram recursos partilhados.  A quantidade de recursos disponíveis na piscina partilhada é calculada da seguinte forma.

[Piscina Partilhada] = 100 - [soma de todos os grupos de `MIN_PERCENTAGE_RESOURCE` carga de trabalho]

O acesso aos recursos na piscina partilhada é atribuído numa base [de importância.](sql-data-warehouse-workload-importance.md)  Os pedidos com o mesmo nível de importância terão acesso a recursos de piscina partilhados numa base inicial/primeira.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: configurar o isolamento da carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CRIAR GRUPO DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Converter classes de recursos em grupos de carga de trabalho](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitorização do Portal de Gestão da Carga de Trabalho.](sql-data-warehouse-workload-management-portal-monitor.md)  
