---
title: Isolamento de cargas de trabalho
description: Orientações para a definição do isolamento da carga de trabalho com grupos de carga de trabalho em Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734785"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics, grupo de trabalho de carga de trabalho

Este artigo explica como os grupos de carga de trabalho podem ser usados para configurar o isolamento da carga de trabalho, conter recursos e aplicar regras de tempo de execução para a execução de consultas.

## <a name="workload-groups"></a>Grupos de carga de trabalho

Os grupos de trabalho são contentores para um conjunto de pedidos e são a base para a forma como a gestão da carga de trabalho, incluindo o isolamento da carga de trabalho, é configurada num sistema.  Os grupos de carga de trabalho são criados utilizando a sintaxe [do GRUPO DE CARGA DE CARGA CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  Uma configuração simples de gestão de carga de trabalho pode gerir cargas de dados e consultas de utilizador.  Por exemplo, um grupo de trabalho nomeado `wgDataLoads` definirá aspetos de carga de trabalho para os dados que estão a ser carregados no sistema. Além disso, um grupo de trabalho nomeado `wgUserQueries` definirá aspetos de carga de trabalho para os utilizadores que executam consultas para ler dados do sistema.

As secções seguintes irão destacar como os grupos de carga de trabalho fornecem a capacidade de definir o isolamento, a contenção, a definição de recursos de pedido e a adesão às regras de execução.

## <a name="workload-isolation"></a>Isolamento de cargas de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de trabalho.  O isolamento da carga de trabalho é conseguido configurando o parâmetro MIN_PERCENTAGE_RESOURCE para maior do que zero na sintaxe [do GRUPO DE CARGA CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  Para cargas de trabalho de execução contínua que precisam aderir a SLAs apertados, o isolamento garante que os recursos estão sempre disponíveis para o grupo de carga de trabalho.

Configurar o isolamento da carga de trabalho define implicitamente um nível garantido de concordância. Por exemplo, um grupo de carga de trabalho com um `MIN_PERCENTAGE_RESOURCE` conjunto de 30% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` fixado em 2% é garantido 15 concuncy.  O nível de concordância é garantido porque 15-2% de faixas horárias de recursos são reservadas no grupo de carga de trabalho em todos os momentos (independentemente de como `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` é configurado).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` for maior do que e for maior do que os recursos `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `CAP_PERCENTAGE_RESOURCE` `MIN_PERCENTAGE_RESOURCE` adicionais são adicionados por pedido.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` e for igual e for maior do `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `CAP_PERCENTAGE_RESOURCE` `MIN_PERCENTAGE_RESOURCE` que, a concordância adicional é possível.  Considere o método abaixo para determinar a concordância garantida:

[Concurrency garantido] = [ `MIN_PERCENTAGE_RESOURCE` [ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Existem valores mínimos viáveis de nível de serviço específicos para min_percentage_resource.  Para mais informações, consulte [Valores Efetivos](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values) para mais detalhes.

Na ausência de isolamento da carga de trabalho, os pedidos operam no [conjunto partilhado](#shared-pool-resources) de recursos.  O acesso aos recursos na piscina partilhada não está garantido e é atribuído numa base [de importância.](sql-data-warehouse-workload-importance.md)

A configuração do isolamento da carga de trabalho deve ser feita com cautela, uma vez que os recursos são atribuídos ao grupo de trabalho, mesmo que não existam pedidos ativos no grupo de trabalho. O isolamento excessivo pode levar a uma menor utilização geral do sistema.

Os utilizadores devem evitar uma solução de gestão da carga de trabalho que configura um isolamento 100% da carga de trabalho: o isolamento a 100% é alcançado quando a soma de min_percentage_resource configurada em todos os grupos de carga de trabalho é igual a 100%.  Este tipo de configuração é excessivamente restritivo e rígido, deixando pouca margem para pedidos de recursos que são acidentalmente classificados indevidamente. Existe uma disposição que permite a execução de um pedido de execução de grupos de trabalho não configurados para isolamento. Os recursos atribuídos a este pedido aparecerão como um zero nos DMVs dos sistemas e pedirão emprestado um pequeno nível de concessão de recursos do sistema de recursos reservados.

> [!NOTE]
> Para garantir uma utilização ótima dos recursos, considere uma solução de gestão da carga de trabalho que aproveite algum isolamento para garantir que as SLAs sejam satisfeitas e misturadas com recursos partilhados que são acedidos com base na [importância da carga de trabalho.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Contenção da carga de trabalho

A contenção da carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção da carga de trabalho é conseguida configurando o parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe do [GRUPO DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  CREATE.  Considere o cenário em que os utilizadores precisam de ler o acesso ao sistema para que possam executar uma análise do "e se" através de consultas ad-hoc.  Este tipo de pedidos pode ter um impacto negativo em outras cargas de trabalho que estão a decorrer no sistema.  A configuração da contenção garante que a quantidade de recursos é limitada.

Configurar a contenção da carga de trabalho define implicitamente um nível máximo de concordância.  Com um CAP_PERCENTAGE_RESOURCE definido para 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido para 1%, até um nível de 60-concuncy é permitido para o grupo de carga de trabalho.  Considere o método abaixo incluído para determinar a máxima concordância:

[Concurrency] = [ `CAP_PERCENTAGE_RESOURCE` [ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> A CAP_PERCENTAGE_RESOURCE efetiva de um grupo de trabalho não chegará a 100% quando são criados grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE a um nível superior a zero.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter valores de tempo de execução eficazes.

## <a name="resources-per-request-definition"></a>Recursos por definição de pedido

Os grupos de carga de trabalho fornecem um mecanismo para definir a quantidade de recursos min e max que são atribuídos por pedido com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe do [GRUPO DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) CREATE.  Neste caso, os recursos são CPU e memória.  A configuração destes valores dita quantos recursos e que nível de concordância podem ser alcançados no sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que predefine o mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como escolher uma classe de recursos, configurar REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor dos recursos utilizados por um pedido.  O montante dos recursos indicados pelo valor fixo está garantido para a atribuição ao pedido antes do início da execução.  Para os clientes que migram de classes de recursos para grupos de carga de trabalho, considere seguir o artigo [como mapear](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) das classes de recursos para grupos de carga de trabalho como ponto de partida.

Configurar REQUEST_MAX_RESOURCE_GRANT_PERCENT a um valor superior ao REQUEST_MIN_RESOURCE_GRANT_PERCENT permite ao sistema alocar mais recursos por pedido.  Ao agendar um pedido, o sistema determina a atribuição de recursos reais ao pedido, que é entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade de recursos em pool partilhado e carga atual no sistema.  Os recursos devem existir no conjunto de recursos [partilhados](#shared-pool-resources) quando a consulta está agendada.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT têm valores efetivos que dependem dos valores efetivos de MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter valores de tempo de execução eficazes.

## <a name="execution-rules"></a>Regras de Execução

Nos sistemas de reporte ad-hoc, os clientes podem acidentalmente executar consultas em fuga que impactam severamente a produtividade de outros.  Os administradores do sistema são forçados a passar o tempo a matar consultas em fuga para libertar recursos do sistema.  Os grupos de carga de trabalho oferecem a capacidade de configurar uma regra de tempo de execução de consulta para cancelar consultas que tenham excedido o valor especificado.  A regra é configurada definindo o `QUERY_EXECUTION_TIMEOUT_SEC` parâmetro na sintaxe [do GRUPO DE CARGA CREATE.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="shared-pool-resources"></a>Recursos de piscina compartilhados

Os recursos partilhados do pool são os recursos não configurados para o isolamento.  Grupos de carga de trabalho com um MIN_PERCENTAGE_RESOURCE definidos para zero recursos de alavancagem na piscina partilhada para executar pedidos.  Grupos de carga de trabalho com um CAP_PERCENTAGE_RESOURCE superior ao MIN_PERCENTAGE_RESOURCE também utilizaram recursos partilhados.  A quantidade de recursos disponíveis na piscina partilhada é calculada da seguinte forma.

[Piscina Partilhada] = 100 - [soma de todos os grupos de `MIN_PERCENTAGE_RESOURCE` carga de trabalho]

O acesso aos recursos na piscina partilhada é atribuído numa base [de importância.](sql-data-warehouse-workload-importance.md)  Os pedidos com o mesmo nível de importância terão acesso aos recursos partilhados do pool numa base inicial de in/first out.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: configurar o isolamento da carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CRIAR GRUPO DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Converter classes de recursos em grupos de carga de trabalho.](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [Monitorização do Portal de Gestão da Carga de Trabalho](sql-data-warehouse-workload-management-portal-monitor.md).  
