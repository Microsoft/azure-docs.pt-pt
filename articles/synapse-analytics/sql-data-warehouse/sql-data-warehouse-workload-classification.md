---
title: Classificação da carga de trabalho
description: Orientação para a utilização da classificação para gerir a conmoeda, a importância e a computação de recursos para consultas em Azure Synapse Analytics.
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
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743652"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Classificação da carga de trabalho da Azure Synapse Analytics

Este artigo explica o processo de classificação da carga de trabalho de atribuição de um grupo de carga de trabalho e importância para os pedidos de entrada com piscinas SYnapse SQL em Azure Synapse.

## <a name="classification"></a>Classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A classificação da gestão da carga de trabalho permite aplicar políticas de carga de trabalho aos pedidos através da atribuição de classes de [recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância.](sql-data-warehouse-workload-importance.md)

Embora existam muitas formas de classificar as cargas de trabalho de armazenamento de dados, a classificação mais simples e comum é a carga e a consulta. Carrega os dados com inserção, atualização e eliminações.  Consulta os dados utilizando seleciona. Uma solução de armazenamento de dados terá muitas vezes uma política de carga para a atividade de carga, como a atribuição de uma classe de recursos mais elevada com mais recursos. Uma política de carga de trabalho diferente poderia aplicar-se a consultas, tais como menor importância em comparação com as atividades de carga.

Também pode subclassificar a sua carga e consulta. A subclassificação dá-lhe mais controlo sobre as suas cargas de trabalho. Por exemplo, as cargas de trabalho de consulta podem consistir em refrescos de cubos, consultas de tablier ou consultas ad-hoc. Pode classificar cada uma destas cargas de trabalho de consulta com diferentes classes de recursos ou configurações de importância. A carga também pode beneficiar da subclassificação. Grandes transformações podem ser atribuídas a classes de recursos maiores. Uma maior importância pode ser usada para garantir que os dados de vendas chave são carregadores antes de dados meteorológicos ou um feed de dados sociais.

Nem todas as declarações são classificadas porque não requerem recursos ou precisam de importância para influenciar a execução.  As declarações de dbcc, BEGIN, COMMIT e ROLLBACK TRANSACTION não são classificadas.

## <a name="classification-process"></a>Processo de classificação

A classificação para o pool SQL Synapse em Azure Synapse é alcançada hoje, atribuindo aos utilizadores uma função que tem uma classe de recursos correspondente atribuída a ela usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). A capacidade de caracterizar pedidos para além de um login para uma classe de recursos é limitada a esta capacidade. Um método mais rico para a classificação está agora disponível com a sintaxe [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Com esta sintaxe, os utilizadores de piscinaS SYnapse SQL podem atribuir `workload_group` importância e quanto recursos do sistema são atribuídos a um pedido através do parâmetro.

> [!NOTE]
> A classificação é avaliada por pedido. Vários pedidos numa única sessão podem ser classificados de forma diferente.

## <a name="classification-weighting"></a>Ponderação de classificação

Como parte do processo de classificação, a ponderação está em vigor para determinar qual o grupo de carga de trabalho atribuído.  A ponderação é a seguinte:

|Parâmetro de classificação |Peso   |
|---------------------|---------|
|NOME DO MEMBRO:UTILIZADOR      |64       |
|NOME MEMBRO:FUNÇÃO      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

O `membername` parâmetro é obrigatório.  No entanto, se o nome de membro especificado for um utilizador de base de dados em vez de uma função de base de dados, a ponderação para o utilizador é maior e, portanto, o classificador é escolhido.

Se um utilizador for membro de várias funções com diferentes classes de recursos atribuídas ou compatíveis em vários classificadores, o utilizador recebe a atribuição da classe de recursos mais alta.  Este comportamento é consistente com o comportamento de atribuição de classe de recursos existente.

## <a name="system-classifiers"></a>Classificadores do sistema

A classificação da carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores do sistema mapeiam as adesões de classe de recursos existentes para aatribuição de recursos de classe de recursos com importância normal. Os classificadores do sistema não podem ser abandonados. Para ver os classificadores do sistema, pode executar a consulta abaixo:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Misturar atribuições de classe de recursos com classificadores

Os classificadores de sistema criados em seu nome proporcionam um caminho fácil para migrar para a classificação da carga de trabalho. Usar mapeamentos de papéis de classe de recursos com precedência de classificação, pode levar a uma classificação errada à medida que começa a criar novos classificadores com importância.

Considere o seguinte cenário:

- Um armazém de dados existente tem um utilizador de base de dados DBAUser atribuído ao papel de classe de recursos maiores. A atribuição da classe de recursos foi feita com sp_addrolemember.
- O armazém de dados está agora atualizado com a gestão da carga de trabalho.
- Para testar a nova sintaxe de classificação, a função de base de dados DBARole (da qual o DBAUser é membro), tem um classificador criado para eles mapeando-os para médio e de alta importância.
- Quando o DBAUser iniciar sessão e fizer uma consulta, a consulta será atribuída a maiorc. Porque um utilizador tem precedência sobre uma adesão a um papel.

Para simplificar a resolução de problemas, recomendamos que remova mapeamentos de papéis de classe de recursos à medida que cria classificadores de carga de trabalho.  O código abaixo retorna os membros de classe de recursos existentes.  Executar [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para cada nome de membro devolvido da classe de recursos correspondente.

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

- Para obter mais informações sobre a criação de um classificador, consulte a [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Consulte o Quickstart sobre como criar um classificador de carga de [trabalho Criar um classificador](quickstart-create-a-workload-classifier-tsql.md)de carga de trabalho .
- Consulte os artigos de como [configurar](sql-data-warehouse-how-to-configure-workload-importance.md) a importância da carga de trabalho e como gerir e monitorizar a Gestão da [Carga de Trabalho.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Veja [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dm_pdw_exec_requests para ver as consultas e a importância atribuída.
