---
title: Isolamento das cargas de trabalho
description: Diretrizes para definir o isolamento de carga de trabalho com grupos de carga de trabalho no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/23/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 86390132be0440b197b680803e5b6032670a7d1c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721035"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL Data Warehouse o isolamento do grupo de carga de trabalho (versão prévia)

Este artigo explica como os grupos de cargas de trabalho podem ser usados para configurar o isolamento de carga de trabalho, conter recursos e aplicar regras de tempo de execução para execução de consulta.

## <a name="workload-groups"></a>Grupos de cargas de trabalho

Grupos de cargas de trabalho são contêineres para um conjunto de solicitações e são a base para como o gerenciamento de carga de trabalho, incluindo o isolamento da carga de trabalho, é configurado em um sistema.  Os grupos de cargas de trabalho são criados usando a sintaxe [Criar grupo de carga de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Uma configuração simples de gerenciamento de carga de trabalho pode gerenciar cargas de dados e consultas de usuário.  Por exemplo, um grupo de carga de trabalho chamado `wgDataLoads` definirá os aspectos de carga de trabalho para os dados que estão sendo carregados no sistema. Além disso, um grupo de carga de trabalho chamado `wgUserQueries` definirá aspectos de carga de trabalho para os usuários que executam consultas para ler dados do sistema.

As seções a seguir destacarão como os grupos de cargas de trabalho fornecem a capacidade de definir isolamento, contenção, solicitar definição de recursos e aderir às regras de execução.

## <a name="workload-isolation"></a>Isolamento das cargas de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de cargas de trabalho.  O isolamento da carga de trabalho é alcançado configurando o parâmetro MIN_PERCENTAGE_RESOURCE para maior do que zero na sintaxe create [workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Para cargas de trabalho de execução contínua que precisam aderir a SLAs rígidos, o isolamento garante que os recursos estejam sempre disponíveis para o grupo de carga de trabalho. 

Configurar o isolamento de carga de trabalho implicitamente define um nível garantido de simultaneidade. Por exemplo, um grupo de carga de trabalho com um `MIN_PERCENTAGE_RESOURCE` fixado em 30% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` fixado para 2% é garantido 15 conmoeda.  O nível de moeda é garantido porque 15-2% de slots de recursos são reservados no grupo de carga de trabalho em todos os momentos (independentemente da configuração `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` for superior a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` e `CAP_PERCENTAGE_RESOURCE` for superior ao `MIN_PERCENTAGE_RESOURCE` recursos adicionais são adicionados por pedido.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` forem iguais e `CAP_PERCENTAGE_RESOURCE` for maior do que `MIN_PERCENTAGE_RESOURCE`, é possível uma moeda adicional.  Considere o método abaixo para determinar a simultaneidade garantida:

[Concurrency Garantida] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Existem valores mínimos de nível de serviço viáveis para min_percentage_resource.  Para obter mais informações, consulte [valores efetivos](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) para obter mais detalhes.

Na ausência de isolamento de carga de trabalho, as solicitações operam no [pool compartilhado](#shared-pool-resources) de recursos.  O acesso a recursos no pool compartilhado não é garantido e é atribuído de acordo com a [importância](sql-data-warehouse-workload-importance.md) .

Configurar o isolamento de carga de trabalho deve ser feito com cautela, pois os recursos são alocados para o grupo de cargas de trabalho, mesmo que não haja nenhuma solicitação ativa no grupo de carga de trabalho. O isolamento de excesso de configuração pode levar à redução geral da utilização do sistema.

Os utilizadores devem evitar uma solução de gestão da carga de trabalho que consuma 100% de isolamento da carga de trabalho: o isolamento 100% é alcançado quando a soma de min_percentage_resource configurada em todos os grupos de carga de trabalho equivale a 100%.  Esse tipo de configuração é excessivamente restritivo e rígido, deixando pouco espaço para as solicitações de recursos que são acidentalmente incorretas. Há uma provisão para permitir que uma solicitação seja executada a partir de grupos de carga de trabalho não configurados para isolamento. Os recursos alocados a essa solicitação aparecerão como zero nos DMVs dos sistemas e emprestando um nível smallrc de concessão de recurso de recursos reservados do sistema.

> [!NOTE] 
> Para garantir a melhor utilização de recursos, considere uma solução de gerenciamento de carga de trabalho que aproveita algum isolamento para garantir que os SLAs sejam atendidos e misturados com recursos compartilhados que são acessados com base na [importância da carga de trabalho](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Contenção de carga de trabalho

A contenção de carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção da carga de trabalho é conseguida configurando o parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Considere o cenário no qual os usuários precisam de acesso de leitura ao sistema para que possam executar uma análise hipotética por meio de consultas ad hoc.  Esses tipos de solicitações podem ter um impacto negativo em outras cargas de trabalho em execução no sistema.  Configurar a contenção garante que a quantidade de recursos seja limitada.

Configurar a contenção de carga de trabalho implicitamente define um nível máximo de simultaneidade.  Com um CAP_PERCENTAGE_RESOURCE fixado para 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido para 1%, até um nível de 60-concurrency é permitido para o grupo de carga de trabalho.  Considere o método incluído abaixo para determinar a simultaneidade máxima:

[Max Concurrency] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Os CAP_PERCENTAGE_RESOURCE efetivos de um grupo de carga de trabalho não chegarão a 100% quando forem criados grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE a um nível superior a zero.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para valores eficazes do tempo de funcionano.

## <a name="resources-per-request-definition"></a>Recursos por definição de solicitação

Os grupos de carga de trabalho fornecem um mecanismo para definir o min e a quantidade máxima de recursos que são atribuídos por pedido com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Os recursos, nesse caso, são CPU e memória.  Configurar esses valores determina a quantidade de recursos e o nível de simultaneidade que podem ser obtidos no sistema.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que não se aplica ao mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como escolher uma classe de recursos, configurar REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor para os recursos utilizados por um pedido.  A quantidade de recursos indicados pelo valor definido é garantida para a alocação para a solicitação antes de começar a execução.  Para clientes que migram de classes de recursos para grupos de carga de trabalho, considere seguir o artigo [como](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) fazer para mapear de classes de recursos para grupos de carga de trabalho como um ponto de partida.

Configurar REQUEST_MAX_RESOURCE_GRANT_PERCENT a um valor superior ao REQUEST_MIN_RESOURCE_GRANT_PERCENT permite ao sistema alocar mais recursos por pedido.  Ao agendar um pedido, o sistema determina a atribuição real de recursos ao pedido, que é entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade de recursos em pool partilhado e carga atual no sistema.  Os recursos devem existir no [pool compartilhado](#shared-pool-resources) de recursos quando a consulta for agendada.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT têm valores efetivos que dependem dos valores MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE eficazes.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para valores eficazes do tempo de funcionano.

## <a name="execution-rules"></a>Regras de execução

Em sistemas de relatórios ad hoc, os clientes podem acidentalmente executar consultas de fuga que afetam seriamente a produtividade de outras pessoas.  Os administradores do sistema são forçados a gastar tempo eliminando as consultas de fuga para liberar recursos do sistema.  Os grupos de cargas de trabalho oferecem a capacidade de configurar uma regra de tempo limite de execução de consulta para cancelar consultas que excederam o valor especificado.  A regra é configurada através da definição do parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [create workload GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Recursos do pool compartilhado

Os recursos do pool compartilhado são os recursos não configurados para isolamento.  Grupos de carga de trabalho com um MIN_PERCENTAGE_RESOURCE definido para zero recursos de alavancagem na piscina partilhada para executar pedidos.  Os grupos de trabalho com CAP_PERCENTAGE_RESOURCE maiores do que MIN_PERCENTAGE_RESOURCE também utilizaram recursos partilhados.  A quantidade de recursos disponíveis no pool compartilhado é calculada da seguinte maneira.

[Piscina Partilhada] = 100 - [soma de `MIN_PERCENTAGE_RESOURCE` em todos os grupos de carga de trabalho]

O acesso aos recursos no pool compartilhado é alocado de acordo com a [importância](sql-data-warehouse-workload-importance.md) .  As solicitações com o mesmo nível de importância acessarão os recursos do pool compartilhado em uma base/primeiro a sair.

## <a name="next-steps"></a>Passos seguintes

- [Início rápido: configurar o isolamento de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CRIAR GRUPO DE CARGA DE TRABALHO](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Converter classes de recursos em grupos de cargas de trabalho](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitoramento de carga de trabalho portal de gerenciamento](sql-data-warehouse-workload-management-portal-monitor.md).  
