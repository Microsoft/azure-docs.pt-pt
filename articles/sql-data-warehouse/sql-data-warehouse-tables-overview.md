---
title: Projetando tabelas
description: Introdução à criação de tabelas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220d3adb31005551b6358034207f1071065b1a7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692394"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Criando tabelas no Azure SQL Data Warehouse

Aprenda os principais conceitos para a criação de tabelas no Azure SQL Data Warehouse. 

## <a name="determine-table-category"></a>Determinar a categoria da tabela 

Um [esquema em estrela](https://en.wikipedia.org/wiki/Star_schema) organiza dados em tabelas de fatos e dimensões. Algumas tabelas são usadas para dados de integração ou de preparo antes de serem movidas para uma tabela de fatos ou dimensões. Conforme você cria uma tabela, decida se os dados da tabela pertencem a um fato, dimensão ou tabela de integração. Essa decisão informa a estrutura e a distribuição da tabela apropriada. 

- As **tabelas de fatos** contêm dados quantitativos que geralmente são gerados em um sistema transacional e, em seguida, são carregados no data warehouse. Por exemplo, uma empresa de varejo gera transações de vendas todos os dias e, em seguida, carrega os dados em uma tabela de fatos data warehouse para análise.

- As **tabelas de dimensões** contêm dados de atributos que podem ser alterados, mas geralmente são alterados com pouca frequência. Por exemplo, o nome e o endereço de um cliente são armazenados em uma tabela de dimensão e atualizados somente quando o perfil do cliente é alterado. Para minimizar o tamanho de uma tabela de fatos grande, o nome e o endereço do cliente não precisam estar em todas as linhas de uma tabela de fatos. Em vez disso, a tabela de fatos e a tabela de dimensões podem compartilhar uma ID de cliente. Uma consulta pode unir as duas tabelas para associar o perfil e as transações de um cliente. 

- As **tabelas de integração** fornecem um local para a integração ou o preparo de dados. Você pode criar uma tabela de integração como uma tabela regular, uma tabela externa ou uma tabela temporária. Por exemplo, você pode carregar dados em uma tabela de preparo, executar transformações nos dados em preparo e, em seguida, inserir os dados em uma tabela de produção.

## <a name="schema-and-table-names"></a>Nomes de tabela e esquema
Os esquemas são uma boa maneira de agrupar tabelas, usadas de maneira semelhante, em conjunto.  Se você estiver migrando vários bancos de dados de uma solução local para SQL Data Warehouse, funcionará melhor migrar todas as tabelas de fatos, dimensões e integração para um esquema no SQL Data Warehouse. Por exemplo, você pode armazenar todas as tabelas no exemplo [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) data warehouse em um esquema chamado WWI. O código a seguir cria um [esquema definido pelo usuário](/sql/t-sql/statements/create-schema-transact-sql) chamado WWI.

```sql
CREATE SCHEMA wwi;
```

Para mostrar a organização das tabelas em SQL Data Warehouse, você pode usar fact, Dim e int como prefixos para os nomes de tabela. A tabela a seguir mostra alguns dos nomes de esquema e de tabela para WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Tipo de tabela | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Localidade | Dimensão | WWI. DimCity |
| Encomenda | Facto | WWI. FactOrder |


## <a name="table-persistence"></a>Persistência de tabela 

As tabelas armazenam dados permanentemente no armazenamento do Azure, temporariamente no armazenamento do Azure ou em um armazenamento de dados externo a data warehouse.

### <a name="regular-table"></a>Tabela regular

Uma tabela regular armazena dados no armazenamento do Azure como parte do data warehouse. A tabela e os dados persistem independentemente se uma sessão está aberta.  Este exemplo cria uma tabela regular com duas colunas. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela temporária
Uma tabela temporária só existe durante a sessão. Você pode usar uma tabela temporária para impedir que outros usuários vejam resultados temporários e também para reduzir a necessidade de limpeza.  As tabelas temporárias utilizam o armazenamento local para oferecer um desempenho rápido.  Para obter mais informações, consulte [tabelas temporárias](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela externa
Uma tabela externa aponta para os dados localizados no blob de armazenamento do Azure ou Azure Data Lake Store. Quando usado em conjunto com a instrução CREATE TABLE AS SELECT, a seleção de uma tabela externa importa dados para SQL Data Warehouse. As tabelas externas são, portanto, úteis para carregar dados. Para obter um tutorial de carregamento, consulte [usar o polybase para carregar dados do armazenamento de BLOBs do Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Tipos de dados
SQL Data Warehouse dá suporte aos tipos de dados usados com mais frequência. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados no CREATE TABLE referência](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. Para obter orientação sobre como usar tipos de dados, consulte [tipos de dados](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas
Um recurso fundamental do SQL Data Warehouse é a maneira como ele pode armazenar e operar em tabelas entre [distribuições](massively-parallel-processing-mpp-architecture.md#distributions).  O SQL Data Warehouse dá suporte a três métodos de distribuição de dados, Round-Robin (padrão), hash e replicados.

### <a name="hash-distributed-tables"></a>Tabelas distribuídas por hash
Uma tabela distribuída por hash distribui linhas com base no valor na coluna de distribuição. Uma tabela distribuída por hash foi projetada para alcançar alto desempenho para consultas em tabelas grandes. Há vários fatores a serem considerados ao escolher uma coluna de distribuição. 

Para obter mais informações, consulte [diretrizes de design para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela replicada tem uma cópia completa da tabela disponível em cada nó de computação. As consultas são executadas rapidamente em tabelas replicadas, pois as junções em tabelas replicadas não exigem a movimentação de dados. No entanto, a replicação requer armazenamento extra e não é prática para tabelas grandes. 

Para obter mais informações, consulte [diretrizes de design para tabelas replicadas](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabelas Round Robin
Uma tabela Round Robin distribui linhas de tabela uniformemente em todas as distribuições. As linhas são distribuídas aleatoriamente. O carregamento de dados em uma tabela Round Robin é rápido.  No entanto, as consultas podem exigir mais movimentação de dados do que os outros métodos de distribuição. 

Para obter mais informações, consulte [diretrizes de design para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Métodos de distribuição comuns para tabelas
A categoria da tabela geralmente determina qual opção escolher para distribuir a tabela. 

| Categoria da tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Facto           | Use a distribuição de hash com o índice columnstore clusterizado. O desempenho melhora quando duas tabelas de hash são Unidas na mesma coluna de distribuição. |
| Dimensão      | Use replicado para tabelas menores. Se as tabelas forem muito grandes para serem armazenadas em cada nó de computação, use o hash distribuído. |
| Teste        | Use Round Robin para a tabela de preparo. A carga com CTAS é rápida. Depois que os dados estiverem na tabela de preparo, use INSERT... Selecione para mover os dados para tabelas de produção. |

## <a name="table-partitions"></a>Partições da tabela
Uma tabela particionada armazena e executa operações nas linhas da tabela de acordo com os intervalos de dados. Por exemplo, uma tabela pode ser particionada por dia, mês ou ano. Você pode melhorar o desempenho da consulta por meio de eliminação de partição, o que limita uma verificação de consulta aos dados em uma partição. Você também pode manter os dados por meio da alternância de partição. Como os dados no SQL Data Warehouse já estão distribuídos, muitas partições podem reduzir o desempenho da consulta. Para obter mais informações, consulte [diretrizes de particionamento](sql-data-warehouse-tables-partition.md).  Quando a partição muda para partições de tabela que não estão vazias, considere usar a opção TRUNCATE_TARGET na instrução [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) se os dados existentes forem truncados. O código abaixo alterna os dados diários transformados para o SalesFact, substituindo os dados existentes. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Índices Columnstore
Por padrão, o SQL Data Warehouse armazena uma tabela como um índice columnstore clusterizado. Essa forma de armazenamento de dados atinge alta compactação de dados e desempenho de consulta em tabelas grandes.  O índice columnstore clusterizado geralmente é a melhor opção, mas, em alguns casos, um índice clusterizado ou um heap é a estrutura de armazenamento apropriada.  Uma tabela de heap pode ser especialmente útil para carregar dados transitórios, como uma tabela de preparo que é transformada em uma tabela final.

Para obter uma lista de recursos columnstore, consulte [novidades para índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Para melhorar o desempenho do índice columnstore, consulte [maximizando a qualidade do rowgroup para índices columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Estatísticas
O otimizador de consulta usa estatísticas em nível de coluna quando cria o plano para executar uma consulta. Para melhorar o desempenho da consulta, é importante ter estatísticas em colunas individuais, especialmente colunas usadas em junções de consulta. A [criação de estatísticas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) ocorre automaticamente.  No entanto, a atualização de estatísticas não ocorre automaticamente. Atualize as estatísticas depois que um número significativo de linhas for adicionado ou alterado. Por exemplo, atualize estatísticas após uma carga. Para obter mais informações, consulte [diretrizes de estatísticas](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Chave primária e chave exclusiva
Só há suporte para a chave primária quando não CLUSTERIZAdo e não imposto são usados.  Só há suporte para a restrição UNIQUE quando não imposta é usado.  Verifique [SQL data warehouse restrições de tabela](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Comandos para criar tabelas
Você pode criar uma tabela como uma nova tabela vazia. Você também pode criar e popular uma tabela com os resultados de uma instrução SELECT. A seguir estão os comandos T-SQL para criar uma tabela.

| Instrução T-SQL | Descrição |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Cria uma tabela vazia definindo todas as colunas e opções da tabela. |
| [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) | Cria uma tabela externa. A definição da tabela é armazenada em SQL Data Warehouse. Os dados da tabela são armazenados no armazenamento de BLOBs do Azure ou Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Popula uma nova tabela com os resultados de uma instrução SELECT. As colunas de tabela e os tipos de dados são baseados nos resultados da instrução SELECT. Para importar dados, essa instrução pode selecionar de uma tabela externa. |
| [CRIAR TABELA EXTERNA COMO SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Cria uma nova tabela externa exportando os resultados de uma instrução SELECT para um local externo.  O local é o armazenamento de BLOBs do Azure ou o Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Alinhando dados de origem com o data warehouse

As tabelas do data warehouse são preenchidas com o carregamento de dados de outra fonte de dados. Para executar uma carga bem-sucedida, o número e os tipos de dados das colunas nos dados de origem devem ser alinhados com a definição de tabela no data warehouse. Obter os dados para alinhar pode ser a parte mais difícil de criar tabelas. 

Se os dados forem provenientes de vários armazenamentos de dados, você poderá colocá-los na data warehouse e armazená-los em uma tabela de integração. Depois que os dados estiverem na tabela de integração, você poderá usar o poder de SQL Data Warehouse para executar operações de transformação. Depois que os dados estiverem preparados, você poderá inseri-los em tabelas de produção.

## <a name="unsupported-table-features"></a>Recursos de tabela sem suporte
O SQL Data Warehouse dá suporte a muitos, mas não a todos, dos recursos de tabela oferecidos por outros bancos de dados.  A lista a seguir mostra alguns dos recursos de tabela que não têm suporte no SQL Data Warehouse.

- Chave estrangeira, verificar [restrições de tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)
- [Colunas computadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Exibições indexadas](/sql/relational-databases/views/create-indexed-views)
- [Ordem](/sql/t-sql/statements/create-sequence-transact-sql)
- [Colunas esparsas](/sql/relational-databases/tables/use-sparse-columns)
- Chaves substitutas. Implementar com [identidade](sql-data-warehouse-tables-identity.md).
- [Sinônimos](/sql/t-sql/statements/create-synonym-transact-sql)
- [Acionadores](/sql/t-sql/statements/create-trigger-transact-sql)
- [Índices exclusivos](/sql/t-sql/statements/create-index-transact-sql)
- [Tipos definidos pelo usuário](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Consultas de tamanho de tabela
Uma maneira simples de identificar o espaço e as linhas consumidas por uma tabela em cada uma das distribuições 60 é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, o uso de comandos DBCC pode estar bastante limitado.  As DMVs (exibições de gerenciamento dinâmico) mostram mais detalhes do que os comandos DBCC. Comece criando esta exibição.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Resumo do espaço de tabela

Essa consulta retorna as linhas e o espaço por tabela.  Ele permite que você veja quais tabelas são suas maiores tabelas e se elas são de rodízio, replicadas ou distribuídas por hash.  Para tabelas distribuídas por hash, a consulta mostra a coluna de distribuição.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espaço de tabela por tipo de distribuição

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espaço de tabela por tipo de índice

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Resumo do espaço de distribuição

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Passos seguintes
Depois de criar as tabelas para seu data warehouse, a próxima etapa é carregar dados na tabela.  Para obter um tutorial de carregamento, consulte [carregando dados para SQL data warehouse](load-data-wideworldimportersdw.md).
