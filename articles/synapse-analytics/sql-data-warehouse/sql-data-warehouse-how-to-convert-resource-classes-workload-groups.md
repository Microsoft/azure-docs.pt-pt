---
title: Converter classe de recursos para um grupo de carga de trabalho
description: Saiba como criar um grupo de carga de trabalho semelhante a uma classe de recursos numa piscina de SQL dedicada.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728561"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos em grupos de carga de trabalho

Os grupos de trabalho fornecem um mecanismo para isolar e conter recursos do sistema.  Além disso, os grupos de carga de trabalho permitem definir regras de execução para os pedidos que neles são executados.  Uma regra de execução de tempo de consulta permite que consultas em fuga sejam canceladas sem a intervenção do utilizador.  Este artigo explica como fazer uma classe de recursos existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, é adicionada uma regra de tempo de consulta opcional.

> [!NOTE]
> Consulte as atribuições da [classe de recursos de mistura com classificadores](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) no documento do conceito de [classificação da carga de trabalho](sql-data-warehouse-workload-classification.md) para obter orientações sobre a utilização de grupos de carga de trabalho e classes de recursos ao mesmo tempo.

## <a name="understanding-the-existing-resource-class-configuration"></a>Compreender a configuração de classe de recursos existente

Os grupos de carga de trabalho exigem um parâmetro chamado `REQUEST_MIN_RESOURCE_GRANT_PERCENT` que especifica a percentagem de recursos globais do sistema atribuídos por pedido.  A atribuição de recursos é feita para [as classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) através da atribuição de faixas horárias de conuncy.  Para determinar o valor a `REQUEST_MIN_RESOURCE_GRANT_PERCENT` especificar, utilize o <link tbd> DMV sys.dm_workload_management_workload_groups_stats.  Por exemplo, a consulta abaixo devolve um valor que pode ser usado para o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parâmetro para criar um grupo de carga de trabalho semelhante ao staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de carga de trabalho operam com base na percentagem dos recursos globais do sistema.  

Como os grupos de carga de trabalho operam com base na percentagem dos recursos globais do sistema, à medida que se escala e desce, a percentagem de recursos atribuídos a classes de recursos estáticos em relação às alterações globais dos recursos do sistema.  Por exemplo, o staticrc40 no DW1000c atribui 19,2% dos recursos globais do sistema.  No DW2000c, 9,6% são atribuídos.  Este modelo é semelhante se desejar aumentar a sua concordância em vez de atribuir mais recursos por pedido.

## <a name="create-workload-group"></a>Criar Grupo de Trabalho

Com o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` conhecido, pode utilizar a sintaxe do GRUPO DE CARGA DE TRABALHO <link> CREATE para criar o grupo de carga de trabalho.  Pode especificar opcionalmente um `MIN_PERCENTAGE_RESOURCE` que seja maior do que zero para isolar recursos para o grupo de carga de trabalho.  Além disso, pode especificar opcionalmente `CAP_PERCENTAGE_RESOURCE` menos de 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

Utilizando o mediumrc como base para um exemplo, o código abaixo define a `MIN_PERCENTAGE_RESOURCE` dedicão 10% dos recursos do sistema e garante que `wgDataLoads` uma consulta será capaz de executar todo o tempo.  Adicionalmente, `CAP_PERCENTAGE_RESOURCE` está definido para 40% e limita este grupo de carga de trabalho a quatro pedidos simultâneos.  Ao definir o `QUERY_EXECUTION_TIMEOUT_SEC` parâmetro para 3600, qualquer consulta que se prolongue por mais de 1 hora será automaticamente cancelada.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Criar o Classificador

Anteriormente, o mapeamento de consultas às aulas de recursos foi feito com [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Para obter a mesma funcionalidade e mapa pedidos para grupos de carga de trabalho, utilize a sintaxe [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  A utilização de sp_addrolemember só lhe permitiu mapear recursos para um pedido com base num login.  Um classificador oferece opções adicionais para além do login, tais como:
    - etiqueta
    - sessão
    - tempo O exemplo abaixo atribui consultas do `AdfLogin` login que também têm o RÓTULO DE [OPPÇÃO](sql-data-warehouse-develop-label.md)  definido `factloads` para o grupo de carga de trabalho acima `wgDataLoads` criado.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Teste com uma consulta de amostra

Abaixo está uma consulta de amostra e uma consulta de DMV para garantir que o grupo de carga de trabalho e o classificador estão configurados corretamente.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Próximos passos

- [Isolamento da carga de trabalho](sql-data-warehouse-workload-isolation.md)
- [Como criar um grupo de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CRIAR CLASSIFICAÇÃO DE CARGA DE TRABALHO (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [CRIAR GRUPO DE CARGA DE TRABALHO (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
