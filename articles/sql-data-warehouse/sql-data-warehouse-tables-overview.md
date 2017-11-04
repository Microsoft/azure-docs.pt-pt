---
title: "Descrição geral das tabelas no armazém de dados SQL | Microsoft Docs"
description: "Introdução ao Azure SQL tabelas de armazém de dados."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/29/2016
ms.author: shigu;jrj
ms.openlocfilehash: c16fef2f302dbc56f257eaf2f0d2b68b6a3c1852
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Descrição geral das tabelas no armazém de dados do SQL Server
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Tipos de dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> 
> 

Como começar a criar tabelas no armazém de dados do SQL Server é simple.  O basic [CREATE TABLE] [ CREATE TABLE] sintaxe segue-se a sintaxe comuns que é mais provável que já familiarizado com funcionem com outras bases de dados.  Para criar uma tabela, basta terá da tabela de nomes, nome do seu colunas e definir os tipos de dados para cada coluna.  Se tiver de criar tabelas noutras bases de dados, este deve ter um aspeto familiar para si.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

O exemplo acima cria uma tabela com o nome de clientes com duas colunas, FirstName e LastName.  Cada coluna é definida com um tipo de dados de VARCHAR(25), o que limita os dados de 25 carateres.  Estes atributos fundamentais de uma tabela, bem como outras pessoas, principalmente são os mesmos que outras bases de dados.  Tipos de dados estão definidos para cada coluna e certifique-se a integridade dos dados.  Os índices podem ser adicionados para melhorar o desempenho ao reduzir a e/s.  Pode ser adicionada a criação de partições para melhorar o desempenho quando necessitar de modificar os dados.

[Mudar o nome] [ RENAME] uma tabela de armazém de dados do SQL Server tem o seguinte aspeto:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tabelas distribuídas
Um novo atributo fundamental que foi introduzido pela sistemas distribuídos, tal como o SQL Data Warehouse é o **coluna distribuição**.  A coluna de distribuição é muito que-SOA como.  É a coluna que determina como distribuir ou dividir os dados em segundo plano.  Quando cria uma tabela sem a especificação de coluna de distribuição, a tabela é automaticamente distribuída utilizando **round robin**.  Embora o round robin tabelas possam ser suficientes em alguns cenários, definir colunas de distribuição pode reduzir significativamente o movimento de dados durante consultas, deste modo, otimizar o desempenho.  Em situações em que exista uma pequena quantidade de dados numa tabela, escolher criar a tabela com o **replicar** tipo de distribuição copia dados para cada nó de computação e guarda o movimento de dados no momento de execução da consulta. Consulte [distribuir uma tabela] [ Distribute] para saber mais sobre como selecionar uma coluna de distribuição.

## <a name="indexing-and-partitioning-tables"></a>A indexação e tabelas de criação de partições
À medida que fica mais avançadas utilizando o SQL Data Warehouse e pretende otimizar o desempenho, poderá ser útil saber mais sobre a estrutura da tabela.  Para obter mais informações, consulte os artigos no [tipos de dados de tabela][Data Types], [distribuir uma tabela][Distribute], [uma tabela de indexação] [ Index] e [uma tabela de criação de partições][Partition].

## <a name="table-statistics"></a>Estatísticas de tabela
As estatísticas são um extremamente importante para obter o melhor desempenho fora do seu SQL Data Warehouse.  Uma vez que o SQL Data Warehouse ainda não foram automaticamente criar e atualizar as estatísticas para si, como podem ter-lhe ter esperar na SQL Database do Azure, ler o nosso artigo sobre [estatísticas] [ Statistics] poderá ser um dos artigos mais importantes para se certificar de que obter o melhor desempenho das consultas de leitura.

## <a name="temporary-tables"></a>Tabelas temporárias
Tabelas temporárias são tabelas que só existem durante o início de sessão e não podem ser visualizadas por outros utilizadores.  Tabelas temporárias podem ser uma boa forma de impedir que outros utilizadores ver resultados temporários e também reduzir a necessidade de limpeza.  Uma vez que a tabelas temporárias também utilizam o armazenamento local, pode oferecem um desempenho mais rápido para algumas operações.  Consulte o [tabela temporária] [ Temporary] artigos para obter mais detalhes sobre a tabelas temporárias.

## <a name="external-tables"></a>Tabelas externas
As tabelas externas, também conhecido como Polybase tabelas, são tabelas que podem ser consultadas do armazém de dados do SQL Server, mas o ponto de dados externas do SQL Data Warehouse.  Por exemplo, pode criar uma tabela externa que aponta para ficheiros no Blob Storage do Azure.  Para obter mais detalhes sobre como criar e consultar uma tabela externa, consulte [carregar dados com o Polybase][Load data with Polybase].  

## <a name="unsupported-table-features"></a>Funcionalidades de tabela não suportado
Ao SQL Data Warehouse contém muitas das funcionalidades de tabela mesmo oferecidas pelas outras bases de dados, existem algumas funcionalidades que ainda não são suportadas.  Segue-se uma lista de algumas das funcionalidades de tabela que ainda não são suportadas.

| Funcionalidades não suportadas |
| --- |
| Chave primária, chaves externas, exclusivos e verificação [restrições de tabela][Table Constraints] |
| [Índices exclusivos][Unique Indexes] |
| [Colunas calculadas][Computed Columns] |
| [Colunas dispersas][Sparse Columns] |
| [Tipos definidos pelo utilizador][User-Defined Types] |
| [Sequência][Sequence] |
| [Acionadores][Triggers] |
| [Vistas indexadas][Indexed Views] |
| [Sinónimos][Synonyms] |

## <a name="table-size-queries"></a>Consultas de tamanho da tabela
Uma forma simples para identificar o espaço e linhas consumidas por uma tabela em cada um das distribuições de 60 consiste em utilizar [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, utilizar comandos DBCC pode ser bastante limitação.  Vistas de gestão dinâmica (DMVs) irão permitir-lhe ver muito mais detalhes, bem como dão-lhe muito maior controlo sobre os resultados da consulta.  Comece por criar nesta vista, o que irá ser referida por muitos dos nossos exemplos neste e outros artigos.

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

### <a name="table-space-summary"></a>Resumo de espaço de tabela
Esta consulta devolve as linhas e espaço por tabela.  É uma excelente consulta para ver as tabelas são suas tabelas de maiores e se são o round robin, replicadas ou distribuídas de hash.  Para as tabelas hash Distribuída também mostra a coluna de distribuição.  Na maioria dos casos, as tabelas maiores devem ser hash distribuída com um índice columnstore em cluster.

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

### <a name="distribution-space-summary"></a>Resumo de espaço de distribuição
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
Para obter mais informações, consulte os artigos no [tipos de dados de tabela][Data Types], [distribuir uma tabela][Distribute], [uma tabela de indexação][Index], [uma tabela de criação de partições][Partition], [manutenção das estatísticas de tabela] [ Statistics] e [tabelas temporárias][Temporary].  Para obter mais informações sobre as melhores práticas, consulte [melhores práticas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
