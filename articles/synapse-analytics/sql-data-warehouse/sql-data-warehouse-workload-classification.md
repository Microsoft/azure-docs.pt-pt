---
title: Classificação da carga de trabalho para piscina SQL dedicada
description: Orientações para a utilização da classificação para gerir a conuscrição de consultas, importância e recursos de cálculo para piscinas SQL dedicadas em Azure Synapse Analytics.
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
ms.openlocfilehash: 7cd3619aa60f1bd8ac13ff767857b44348989285
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678428"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Classificação da carga de trabalho para piscina sql dedicada em Azure Synapse Analytics

Este artigo explica o processo de classificação da carga de trabalho de atribuição de um grupo de carga de trabalho e a importância para os pedidos de entrada com piscinas SQL dedicadas em Azure Synapse.

## <a name="classification"></a>Classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A classificação da gestão da carga de trabalho permite que as políticas de carga de trabalho sejam aplicadas aos pedidos através da atribuição de [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância.](sql-data-warehouse-workload-importance.md)

Embora existam muitas formas de classificar as cargas de trabalho de armazenamento de dados, a classificação mais simples e comum é a carga e consulta. Carregue os dados com inserção, atualização e elimine as declarações.  Consulta os dados utilizando as seleções. Uma solução de armazenamento de dados terá muitas vezes uma política de carga de trabalho para a atividade de carga, como atribuir uma classe de recursos mais elevado com mais recursos. Uma política de carga de trabalho diferente poderia aplicar-se a consultas, como uma menor importância em comparação com as atividades de carga.

Também pode subclassificar as cargas de carga e consultar cargas de trabalho. A subclassificação dá-lhe mais controlo das suas cargas de trabalho. Por exemplo, as cargas de trabalho de consulta podem consistir em atualizaçãos de cubos, consultas de tablier ou consultas ad-hoc. Pode classificar cada uma destas cargas de trabalho de consulta com diferentes classes de recursos ou definições de importância. A carga também pode beneficiar da subclassificação. Grandes transformações podem ser atribuídas a classes de recursos maiores. Uma maior importância pode ser usada para garantir que os principais dados de vendas sejam mais onerador antes dos dados meteorológicos ou de um feed de dados sociais.

Nem todas as declarações são classificadas porque não requerem recursos ou precisam de importância para influenciar a execução.  Os comandos DBCC, BEGIN, COMMIT e ROLLBACK TRANSACTION não são classificados.

## <a name="classification-process"></a>Processo de classificação

A classificação para piscina SQL dedicada é alcançada hoje, atribuindo aos utilizadores uma função que tem uma classe de recursos correspondente que lhe seja atribuída utilizando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). A capacidade de caracterizar pedidos para além de um login para uma classe de recursos é limitada com esta capacidade. Um método mais rico para a classificação está agora disponível com a sintaxe [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  Com esta sintaxe, os utilizadores de pools SQL dedicados podem atribuir importância e quantos recursos do sistema são atribuídos a um pedido através do `workload_group` parâmetro.

> [!NOTE]
> A classificação é avaliada por pedido. Vários pedidos numa única sessão podem ser classificados de forma diferente.

## <a name="classification-weighting"></a>Ponderação da classificação

Como parte do processo de classificação, a ponderação está em vigor para determinar qual o grupo de trabalho atribuído.  A ponderação é a seguinte:

|Parâmetro de classificador |Peso   |
|---------------------|---------|
|NOME MEMBRO:UTILIZADOR      |64       |
|NOME-MEMBRO:PAPEL      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

O `membername` parâmetro é obrigatório.  No entanto, se o nome de membro especificado for um utilizador de base de dados em vez de uma função de base de dados, a ponderação para o utilizador é maior e, portanto, o classificador é escolhido.

Se um utilizador for membro de múltiplas funções com diferentes classes de recursos atribuídas ou correspondidas em vários classificadores, o utilizador recebe a atribuição de classe de recursos mais alta.  Este comportamento é consistente com o comportamento de atribuição de classe de recursos existente.

## <a name="system-classifiers"></a>Classificadores de sistema

A classificação da carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores do sistema mapeiam as associações de funções de classe de recursos existentes para alocações de recursos de classe com importância normal. Os classificadores do sistema não podem ser largados. Para visualizar os classificadores do sistema, pode executar a consulta abaixo:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Atribuição de classe de recursos com classificadores

Os classificadores de sistema criados em seu nome fornecem um caminho fácil de migrar para a classificação da carga de trabalho. A utilização de mapeamentos de funções de classe de recursos com precedência de classificação, pode levar à classificação errada à medida que começa a criar novos classificadores com importância.

Considere o seguinte cenário:

- Um armazém de dados existente tem um DBAUser de base de dados atribuído à função de classe de recursos maior. A atribuição da classe de recursos foi feita com sp_addrolemember.
- O armazém de dados é agora atualizado com a gestão da carga de trabalho.
- Para testar a nova sintaxe de classificação, a função de base de dados DBARole (da qual a DBAUser é membro), tem um classificador criado para mapear para eles mapeamento para mediumrc e alta importância.
- Quando o DBAUser iniciar sessão e executa uma consulta, a consulta será atribuída a maiores. Porque um utilizador tem precedência sobre uma adesão de papel.

Para simplificar a classificação errada da resolução de problemas, recomendamos que remova mapeamentos de funções de classe de recursos à medida que cria classificadores de carga de trabalho.  O código abaixo devolve os membros de funções de classe de recursos existentes.  Executar [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para cada nome de membro devolvido da classe de recursos correspondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a criação de um classificador, consulte o [CLASSIFIER CREATE WORKLOAD (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Consulte o Quickstart sobre como criar um classificador de carga de trabalho [Crie um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md).
- Consulte os artigos de como configurar a [importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como gerir e monitorizar a [Gestão da Carga de Trabalho.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Consulte [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para visualizar as consultas e a importância atribuída.
