---
title: Converter classe de recurso em um grupo de carga de trabalho
description: Saiba como criar um grupo de carga de trabalho semelhante a uma classe de recurso no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685704"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos em grupos de cargas de trabalho
Os grupos de carga de trabalho fornecem um mecanismo para isolar e conter recursos do sistema.  Além disso, os grupos de cargas de trabalho permitem definir regras de execução para as solicitações em execução nelas.  Uma regra de execução de tempo limite de consulta permite que consultas de fuga sejam canceladas sem intervenção do usuário.  Este artigo explica como usar uma classe de recurso existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, uma regra de tempo limite de consulta opcional é adicionada.

## <a name="understanding-the-existing-resource-class-configuration"></a>Compreendendo a configuração da classe de recurso existente
Os grupos de cargas de trabalho exigem um parâmetro chamado `REQUEST_MIN_RESOURCE_GRANT_PERCENT` que especifica a porcentagem de recursos gerais do sistema alocados por solicitação.  A alocação de recursos é feita para [classes de recursos](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) alocando slots de simultaneidade.  Para determinar o valor a ser especificado para `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, use o DMV sys. dm_workload_management_workload_groups_stats <link tbd>.  Por exemplo, a consulta de consulta abaixo retorna um valor que pode ser usado para o parâmetro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para criar um grupo de carga de trabalho semelhante a staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de cargas de trabalho operam com base na porcentagem de recursos gerais do sistema.  

Como os grupos de cargas de trabalho operam com base na porcentagem de recursos gerais do sistema, à medida que você escala e reduz verticalmente, a porcentagem de recursos alocados para classes de recursos estáticos em relação às alterações gerais dos recursos do sistema.  Por exemplo, staticrc40 em DW1000c aloca 9,6% dos recursos gerais do sistema.  Em DW2000c, 19,2% são alocados.  Esse modelo é semelhante se você deseja escalar verticalmente para simultaneidade em vez de alocar mais recursos por solicitação.   

## <a name="create-workload-group"></a>Criar grupo de carga de trabalho
Com o `REQUEST_MIN_RESOURCE_GRANT_PERCENT`conhecido, você pode usar a sintaxe criar grupo de carga de trabalho <link> para criar o grupo de cargas de trabalho.  Opcionalmente, você pode especificar um `MIN_PERCENTAGE_RESOURCE` maior que zero para isolar recursos para o grupo de carga de trabalho.  Além disso, opcionalmente, você pode especificar `CAP_PERCENTAGE_RESOURCE` menor que 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

O exemplo abaixo define o `MIN_PERCENTAGE_RESOURCE` para dedicar 9,6% dos recursos do sistema para `wgDataLoads` e garante que uma consulta poderá ser executada o tempo todo.  Além disso, `CAP_PERCENTAGE_RESOURCE` é definido como 38,4% e limita esse grupo de carga de trabalho a quatro solicitações simultâneas.  Ao definir o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` como 3600, qualquer consulta executada por mais de uma hora será cancelada automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Criar o classificador
Anteriormente, o mapeamento de consultas para classes de recursos foi feito com [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Para obter a mesma funcionalidade e mapear solicitações para grupos de carga de trabalho, use a sintaxe [Create Workload classificador](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  O uso de sp_addrolemember só permitia que você mapeie recursos para uma solicitação com base em um logon.  Um classificador fornece opções adicionais além do logon, como:
    - label
    - sessão
    - Quando o exemplo abaixo atribui consultas do logon de `AdfLogin` que também têm o rótulo de [opção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) definido como `factloads` ao grupo de cargas de trabalho `wgDataLoads` criado acima.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testar com uma consulta de exemplo
Veja abaixo uma consulta de exemplo e uma consulta DMV para garantir que o grupo de carga de trabalho e o classificador estejam configurados corretamente.

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

Isolamento de carga de trabalho-link TBD como criar um grupo de carga de trabalho-link TBD