---
title: Isolamento da carga de trabalho
description: Diretrizes para definir o isolamento de carga de trabalho com grupos de carga de trabalho no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/04/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: a31498ec5459604d89fa72a6f2a003dbc1189eed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685364"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL Data Warehouse o isolamento do grupo de carga de trabalho (versão prévia)

Este artigo explica como os grupos de cargas de trabalho podem ser usados para configurar o isolamento de carga de trabalho, conter recursos e aplicar regras de tempo de execução para execução de consulta.

## <a name="workload-groups"></a>Grupos de cargas de trabalho

Grupos de cargas de trabalho são contêineres para um conjunto de solicitações e são a base para como o gerenciamento de carga de trabalho, incluindo o isolamento da carga de trabalho, é configurado em um sistema.  Os grupos de cargas de trabalho são criados usando a sintaxe [Criar grupo de carga de trabalho](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Uma configuração simples de gerenciamento de carga de trabalho pode gerenciar cargas de dados e consultas de usuário.  Por exemplo, um grupo de carga de trabalho chamado `wgDataLoads` definirá os aspectos de carga de trabalho para os dados que estão sendo carregados no sistema. Além disso, um grupo de carga de trabalho chamado `wgUserQueries` definirá aspectos de carga de trabalho para os usuários que executam consultas para ler dados do sistema.

As seções a seguir destacarão como os grupos de cargas de trabalho fornecem a capacidade de definir isolamento, contenção, solicitar definição de recursos e aderir às regras de execução.

## <a name="workload-isolation"></a>Isolamento da carga de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de cargas de trabalho.  O isolamento da carga de trabalho é obtido com a configuração do parâmetro MIN_PERCENTAGE_RESOURCE como maior que zero na sintaxe [Criar grupo de carga de trabalho](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Para cargas de trabalho de execução contínua que precisam aderir a SLAs rígidos, o isolamento garante que os recursos estejam sempre disponíveis para o grupo de carga de trabalho. 

Configurar o isolamento de carga de trabalho implicitamente define um nível garantido de simultaneidade.  Com um MIN_PERCENTAGE_RESOURCE definido como 30% e REQUEST_MIN_RESOURCE_GRANT_PERCENT definido como 2%, um nível de 15 simultaneidade é garantido para o grupo de cargas de trabalho.  Considere o método abaixo para determinar a simultaneidade garantida:

[Simultaneidade garantida] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Há valores específicos mínimos viáveis de nível de serviço para min_percentage_resource.  Para obter mais informações, consulte [valores efetivos](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) para obter mais detalhes.

Na ausência de isolamento de carga de trabalho, as solicitações operam no [pool compartilhado](#shared-pool-resources) de recursos.  O acesso a recursos no pool compartilhado não é garantido e é atribuído de acordo com a [importância](sql-data-warehouse-workload-importance.md) .

Configurar o isolamento de carga de trabalho deve ser feito com cautela, pois os recursos são alocados para o grupo de cargas de trabalho, mesmo que não haja nenhuma solicitação ativa no grupo de carga de trabalho.  O isolamento de excesso de configuração pode levar à redução geral da utilização do sistema.

Os usuários devem evitar uma solução de gerenciamento de carga de trabalho que configure 100% de isolamento de carga de trabalho: 100% de isolamento é obtido quando a soma de min_percentage_resource configurada em todos os grupos de cargas de trabalho é igual a 100%  Esse tipo de configuração é excessivamente restritivo e rígido, deixando pouco espaço para as solicitações de recursos que são acidentalmente incorretas.  Há uma provisão para permitir que uma solicitação seja executada a partir de grupos de carga de trabalho não configurados para isolamento.  Os recursos alocados a essa solicitação aparecerão como zero nos DMVs dos sistemas e emprestando um nível smallrc de concessão de recurso de recursos reservados do sistema.

> [!NOTE] 
> Para garantir a melhor utilização de recursos, considere uma solução de gerenciamento de carga de trabalho que aproveita algum isolamento para garantir que os SLAs sejam atendidos e misturados com recursos compartilhados que são acessados com base na [importância da carga de trabalho](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Contenção de carga de trabalho

A contenção de carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção de carga de trabalho é obtida com a configuração do parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe [Criar grupo de carga de trabalho](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Considere o cenário no qual os usuários precisam de acesso de leitura ao sistema para que possam executar uma análise hipotética por meio de consultas ad hoc.  Esses tipos de solicitações podem ter um impacto negativo em outras cargas de trabalho em execução no sistema.  Configurar a contenção garante que a quantidade de recursos seja limitada.

Configurar a contenção de carga de trabalho implicitamente define um nível máximo de simultaneidade.  Com um CAP_PERCENTAGE_RESOURCE definido como 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido como 1%, até um nível de simultaneidade 60 é permitido para o grupo de cargas de trabalho.  Considere o método incluído abaixo para determinar a simultaneidade máxima:

[Simultaneidade máxima] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> O CAP_PERCENTAGE_RESOURCE efetivo de um grupo de carga de trabalho não alcançará 100% quando grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE em um nível maior que zero forem criados.  Consulte [Sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para obter valores de tempo de execução efetivos.

## <a name="resources-per-request-definition"></a>Recursos por definição de solicitação

Os grupos de carga de trabalho fornecem um mecanismo para definir a quantidade mínima e máxima de recursos alocados por solicitação com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe [Criar grupo de carga de trabalho](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Os recursos, nesse caso, são CPU e memória.  Configurar esses valores determina a quantidade de recursos e o nível de simultaneidade que podem ser obtidos no sistema.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que usa como padrão o mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Como escolher uma classe de recurso, configurar REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor dos recursos utilizados por uma solicitação.  A quantidade de recursos indicados pelo valor definido é garantida para a alocação para a solicitação antes de começar a execução.  Para clientes que migram de classes de recursos para grupos de carga de trabalho, considere seguir o artigo [como](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) fazer para mapear de classes de recursos para grupos de carga de trabalho como um ponto de partida.

Configurar REQUEST_MAX_RESOURCE_GRANT_PERCENT com um valor maior que REQUEST_MIN_RESOURCE_GRANT_PERCENT permite que o sistema aloque mais recursos por solicitação.  Durante o agendamento de uma solicitação, o sistema determina a alocação de recursos real para a solicitação, que está entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade de recursos no pool compartilhado e na carga atual no sistema.  Os recursos devem existir no [pool compartilhado](#shared-pool-resources) de recursos quando a consulta for agendada.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT têm valores efetivos que dependem dos valores efetivo MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE.  Consulte [Sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) para obter valores de tempo de execução efetivos.

## <a name="execution-rules"></a>Regras de execução

Em sistemas de relatórios ad hoc, os clientes podem acidentalmente executar consultas de fuga que afetam seriamente a produtividade de outras pessoas.  Os administradores do sistema são forçados a gastar tempo eliminando as consultas de fuga para liberar recursos do sistema.  Os grupos de cargas de trabalho oferecem a capacidade de configurar uma regra de tempo limite de execução de consulta para cancelar consultas que excederam o valor especificado.  A regra é configurada definindo o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [Criar grupo de carga de trabalho](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .

## <a name="shared-pool-resources"></a>Recursos do pool compartilhado

Os recursos do pool compartilhado são os recursos não configurados para isolamento.  Grupos de cargas de trabalho com um MIN_PERCENTAGE_RESOURCE definido como zero aproveitam os recursos no pool compartilhado para executar solicitações.  Grupos de cargas de trabalho com um CAP_PERCENTAGE_RESOURCE maior que MIN_PERCENTAGE_RESOURCE também usavam recursos compartilhados.  A quantidade de recursos disponíveis no pool compartilhado é calculada da seguinte maneira.

[Pool compartilhado] = 100-[soma de `MIN_PERCENTAGE_RESOURCE` em todos os grupos de carga de trabalho]

O acesso aos recursos no pool compartilhado é alocado de acordo com a [importância](sql-data-warehouse-workload-importance.md) .  As solicitações com o mesmo nível de importância acessarão os recursos do pool compartilhado em uma base/primeiro a sair.

## <a name="next-steps"></a>Passos seguintes

- [Início rápido: configurar o isolamento de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CRIAR GRUPO DE CARGA DE TRABALHO](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Converter classes de recursos em grupos de cargas de trabalho](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
