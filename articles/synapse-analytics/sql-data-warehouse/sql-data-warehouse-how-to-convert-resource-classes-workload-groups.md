---
title: Converter classe de recursos para um grupo de carga de trabalho
description: Aprenda a criar um grupo de carga de trabalho semelhante a uma classe de recursos no Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d20a811270b89772ab75bc298544a549caa01041
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350456"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos para grupos de carga de trabalho
Os grupos de carga de trabalho fornecem um mecanismo para isolar e conter os recursos do sistema.  Além disso, os grupos de carga de trabalho permitem-lhe definir regras de execução para os pedidos que estão a decorrer neles.  Uma regra de execução de tempo de consulta permite que as consultas em fuga sejam canceladas sem a intervenção do utilizador.  Este artigo explica como tomar uma classe de recursos existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, é adicionada uma regra de tempo de consulta opcional.

## <a name="understanding-the-existing-resource-class-configuration"></a>Compreender a configuração da classe de recursos existente
Os grupos de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` carga de trabalho requerem um parâmetro chamado que especifica a percentagem dos recursos globais do sistema atribuídos por pedido.  A atribuição de recursos é feita para as classes de [recursos,](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) atribuindo slots de moeda.  Para determinar o valor `REQUEST_MIN_RESOURCE_GRANT_PERCENT`a especificar, utilize <link tbd> o DMV sys.dm_workload_management_workload_groups_stats.  Por exemplo, a consulta abaixo devolve um valor que `REQUEST_MIN_RESOURCE_GRANT_PERCENT` pode ser usado para o parâmetro para criar um grupo de carga de trabalho semelhante ao estáticarc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de carga de trabalho operam com base na percentagem dos recursos globais do sistema.  

Dado que os grupos de carga de trabalho operam com base na percentagem dos recursos globais do sistema, à medida que se escala para cima e para baixo, a percentagem de recursos atribuídos a classes de recursos estáticos em relação às alterações globais dos recursos do sistema.  Por exemplo, a estática rc40 em DW1000c atribui 9,6% dos recursos globais do sistema.  Em DW2000c, 19,2% são atribuídos.  Este modelo é semelhante se pretender aumentar para a moeda contra a atribuição de mais recursos por pedido.   

## <a name="create-workload-group"></a>Criar grupo de carga de trabalho
Com o `REQUEST_MIN_RESOURCE_GRANT_PERCENT`conhecido, pode utilizar <link> a sintaxe CREATE WORKLOAD GROUP para criar o grupo de carga de trabalho.  Pode especificar opcionalmente `MIN_PERCENTAGE_RESOURCE` um que é maior do que zero para isolar os recursos para o grupo de carga de trabalho.  Além disso, pode `CAP_PERCENTAGE_RESOURCE` especificar opcionalmente menos de 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

O exemplo abaixo `MIN_PERCENTAGE_RESOURCE` define o dededicar 9,6% `wgDataLoads` dos recursos do sistema e garante que uma consulta será capaz de executar todas as vezes.  Adicionalmente, `CAP_PERCENTAGE_RESOURCE` está fixado em 38,4% e limita este grupo de carga de trabalho a quatro pedidos simultâneos.  Ao definir `QUERY_EXECUTION_TIMEOUT_SEC` o parâmetro para 3600, qualquer consulta que se prolonge por mais de 1 hora será automaticamente cancelada.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Criar o Classificador
Anteriormente, o mapeamento de consultas às aulas de recursos era feito com [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Para obter a mesma funcionalidade e pedidos de mapas para grupos de carga de trabalho, utilize a sintaxe [CREATE WORKLOAD CLASSIFIER.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)  Usar sp_addrolemember só lhe permitiu mapear recursos para um pedido baseado num login.  Um classificador fornece opções adicionais além do login, tais como:
    - label
    - sessão
    - tempo O exemplo abaixo atribui `AdfLogin` consultas do login que também `factloads` têm o `wgDataLoads` RÓTULO DE [OPÇÃO](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) definido para o grupo de carga de trabalho acima criado.

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

## <a name="next-steps"></a>Passos seguintes

Tbd de ligação de isolamento de carga de carga como criar uma tbd de ligação de grupo de carga de trabalho