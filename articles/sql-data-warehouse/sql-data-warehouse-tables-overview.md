---
title: Criando tabelas - Azure SQL Data Warehouse | Documentos da Microsoft
description: Introdução à conceção de tabelas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: cb2261e92e90bef7cdd51b0ebf7a4ed34ca01624
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806238"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Criando tabelas no armazém de dados SQL do Azure

Aprenda conceitos fundamentais para a criação de tabelas no armazém de dados SQL do Azure. 

## <a name="determine-table-category"></a>Determinar a categoria de tabela 

R [esquema em estrela](https://en.wikipedia.org/wiki/Star_schema) organizam os dados em tabelas de fatos e dimensão. Algumas tabelas são utilizadas para dados de integração ou de transição antes de passar para uma tabela de fatos ou dimensões. À medida que concebe uma tabela, decida se os dados da tabela pertencem a um fato, uma dimensão ou uma tabela de integração. Esta decisão informa a estrutura da tabela apropriada e a distribuição. 

- **Tabelas de fatos** contêm dados quantitativos normalmente gerados num sistema transacional e, em seguida, são carregados para o armazém de dados. Por exemplo, uma empresa de varejo gera transações de vendas de todos os dias e, em seguida, carrega os dados para uma tabela de factos de armazém de dados para análise.

- **Tabelas de dimensão** contêm dados de atributos que podem ser alteradas, mas geralmente é alterado com pouca frequência. Por exemplo, nome e o endereço do cliente são armazenados numa tabela de dimensão e atualizados apenas quando perfil o cliente é alterado. Para minimizar o tamanho de uma tabela de fatos maiores, nome e o endereço do cliente não precisa de estar na todas as linhas de uma tabela de fatos. Em vez disso, a tabela de fatos e a tabela de dimensão podem partilhar uma ID de cliente. Uma consulta pode associar as duas tabelas para associar o perfil e as transações do cliente. 

- **Tabelas de integração** fornecer um local para integrar ou dados de teste. Pode criar uma tabela de integração como uma tabela normal, uma tabela externa ou uma tabela temporária. Por exemplo, pode carregar dados para uma tabela de teste, executar transformações nos dados de teste e, em seguida, inserir os dados na tabela de produção.

## <a name="schema-and-table-names"></a>Nomes de tabela e esquema
No SQL Data Warehouse, um armazém de dados é um tipo de base de dados. Todas as tabelas no armazém de dados estão contidas no mesmo banco de dados.  Não é possível associar tabelas em vários armazéns de dados. Este comportamento é diferente do SQL Server, que suporta associações entre bases de dados. 

Numa base de dados do SQL Server, poderá utilizar o fato de, dim, ou integrar para os nomes de esquema. Se estiver a migrar uma base de dados do SQL Server para o SQL Data Warehouse, é melhor para migrar todas as tabelas de fatos, dimensão e de integração para um esquema no SQL Data Warehouse. Por exemplo, poderia armazenar todas as tabelas a [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) armazém de dados de exemplo dentro de um esquema chamada wwi. O código seguinte cria um [esquema definido pelo utilizador](/sql/t-sql/statements/create-schema-transact-sql) chamado wwi.

```sql
CREATE SCHEMA wwi;
```

Para mostrar a organização das tabelas no SQL Data Warehouse, poderia usar o fato, dim e int como prefixos a nomes de tabela. A tabela seguinte mostra alguns dos nomes de tabela e esquema WideWorldImportersDW. Ele compara os nomes no SQL Server com nomes no SQL Data Warehouse. 

| Tabela de WideWorldImportersDW  | Tipo de tabela | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| Localidade | Dimensão | Dimension.City | wwi.DimCity |
| Encomenda | Fatos | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Persistência de tabela 

Tabelas de armazenam dados permanentemente no armazenamento do Azure, temporariamente no armazenamento do Azure ou num arquivo de dados externo para o armazém de dados.

### <a name="regular-table"></a>Tabela normal

Uma tabela normal armazena dados no armazenamento do Azure como parte do armazém de dados. A tabela e os dados persistem independentemente se uma sessão é aberta.  Este exemplo cria uma tabela normal com duas colunas. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela temporária
Só existe uma tabela temporária para a duração da sessão. Pode usar uma tabela temporária para impedir que outros utilizadores vejam os resultados temporários e também para reduzir a necessidade de limpeza.  Uma vez que as tabelas temporárias também utilizam o armazenamento local, podem oferecer um desempenho mais rápido para algumas operações.  Para obter mais informações, consulte [tabelas temporárias](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela externa
Uma tabela externa aponta para dados localizados no blob de armazenamento do Azure ou do Azure Data Lake Store. Quando utilizado em conjunto com a instrução CREATE TABLE AS SELECT, selecionar a partir de uma tabela externa importa os dados para o SQL Data Warehouse. Tabelas externas, portanto, são úteis para carregar dados. Para obter um tutorial de carregamento, veja [utilizar o PolyBase para carregar dados do armazenamento de Blobs do Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Tipos de dados
SQL Data Warehouse suporta o mais usado tipos de dados. Para obter uma lista dos tipos de dados suportados, consulte [tipos de dados na referência de CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. Ajuda a minimizar o tamanho dos tipos de dados para melhorar o desempenho de consulta. Para obter orientações sobre como usar tipos de dados, veja [tipos de dados](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas
Um recurso fundamental do SQL Data Warehouse é a forma como ele pode armazenar e operar em tabelas em 60 [distribuições](massively-parallel-processing-mpp-architecture.md#distributions).  As tabelas são distribuídas através de um método de round robin, hash ou a replicação.

### <a name="hash-distributed-tables"></a>Tabelas distribuídas de hash
A distribuição de hash distribui as linhas com base no valor da coluna de distribuição. A tabela de hash e distribuída foi projetada para atingir um alto desempenho para associações de consulta em tabelas grandes. Há vários fatores que afetam a escolha da coluna de distribuição. 

Para obter mais informações, consulte [documentação de orientação para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tabelas replicadas
Tabelas replicadas tem uma cópia completa da tabela disponível em cada nó de computação. Consultas de execução seja veloz na tabelas replicadas, uma vez que as associações em tabelas replicadas não necessitam de movimento de dados. Replicação requer armazenamento extra, porém e não é prática para tabelas grandes. 

Para obter mais informações, consulte [documentação de orientação para as tabelas replicadas](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabelas round-robin
Uma tabela round-robin distribui as linhas de tabela uniformemente por todas as distribuições. As linhas são distribuídas aleatoriamente. Carregar dados para uma tabela round robin é rápida.  No entanto, as consultas podem exigir mais de movimento de dados que os outros métodos de distribuição. 

Para obter mais informações, consulte [documentação de orientação para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Métodos de distribuição comuns para tabelas
A categoria de tabela geralmente determina qual a opção para escolher para a distribuição da tabela. 

| Categoria de tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Fatos           | Utilize a distribuição de hash com o índice columnstore em cluster. Melhora o desempenho quando duas tabelas de hash são associadas na mesma coluna de distribuição. |
| Dimensão      | Utilize replicado para tabelas de menores. Se tabelas são demasiado grandes para armazenar em cada nó de computação, utilize distribuída por hash. |
| Teste        | Utilize o round robin para a tabela de testes. A carga com CTAS é rápida. Quando os dados estiverem na tabela de teste, utilize INSERT... SELECIONE para mover os dados para tabelas de produção. |

## <a name="table-partitions"></a>Partições da tabela
Uma tabela particionada armazena e executa operações em linhas da tabela, de acordo com os intervalos de dados. Por exemplo, uma tabela pode ser particionada por dia, mês ou ano. Pode melhorar o desempenho de consulta através de eliminação de partições, o que limita uma análise de consulta para dados dentro de uma partição. Também pode manter os dados por meio de alternância de partição. Uma vez que os dados no armazém de dados do SQL já tenham sido distribuídos, demasiadas partições podem abrandar o desempenho de consulta. Para obter mais informações, consulte [documentação de orientação de criação de partições](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Índices Columnstore
Por predefinição, o SQL Data Warehouse armazena uma tabela como um índice columnstore em cluster. Essa forma de armazenamento de dados atinge a compressão de dados de alta e desempenho de consultas em tabelas grandes.  O índice columnstore em cluster costuma ser a melhor opção, mas em alguns casos, um índice em cluster ou um heap é a estrutura de armazenamento adequado.

Para obter uma lista dos recursos de columnstore, consulte [o que há de novo para os índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Para melhorar o desempenho de índice columnstore, veja [maximizando a qualidade de rowgroup nos índices columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Estatísticas
O otimizador de consultas utiliza as estatísticas de nível de coluna quando cria o plano para execução de uma consulta. Para melhorar o desempenho de consulta, é importante criar estatísticas em colunas individuais, especialmente as colunas utilizadas em associações de consulta. Criar e atualizar estatísticas não acontecem automaticamente. [Criar estatísticas](/sql/t-sql/statements/create-statistics-transact-sql) depois de criar uma tabela. Atualize estatísticas após um número significativo de linhas é adicionado ou alterado. Por exemplo, Atualize as estatísticas após uma carga. Para obter mais informações, consulte [orientações de estatísticas](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Comandos para a criação de tabelas
Pode criar uma tabela como uma nova tabela vazia. Também pode criar e preencher uma tabela com os resultados de uma instrução select. Seguem-se os comandos T-SQL para a criação de uma tabela.

| Instrução T-SQL | Descrição |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Cria uma tabela vazia com a definição de todas as colunas da tabela e opções. |
| [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) | Cria uma tabela externa. A definição da tabela é armazenada no SQL Data Warehouse. Os dados de tabela são armazenados no armazenamento de Blobs do Azure ou no Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Preenche uma nova tabela com os resultados de uma instrução select. Os tipos de colunas e dados de tabela baseiam-se nos resultados da instrução select. Para importar dados, essa instrução pode selecionar uma tabela externa. |
| [CRIAR EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Cria uma nova tabela externa ao exportar os resultados de uma instrução select para uma localização externa.  A localização é o armazenamento de Blobs do Azure ou o Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Alinhamento de dados de origem no armazém de dados

Tabelas do armazém de dados são preenchidas com o carregamento de dados de outra origem de dados. Para executar uma carga com êxito, os número e tipos de dados das colunas nos dados de origem devem estar alinhadas com a definição de tabela no armazém de dados. Obtendo os dados para se alinhar pode ser a parte mais difícil da criação de suas tabelas. 

Se estiver a receber dados de vários armazenamentos de dados, pode colocar os dados no armazém de dados e o armazenamos numa tabela de integração. Quando os dados estiverem na tabela de integração, pode utilizar o poder do SQL Data Warehouse para realizar operações de transformação. Depois dos dados são preparados, pode inserir em tabelas de produção.

## <a name="unsupported-table-features"></a>Recursos de tabelas não suportado
O SQL Data Warehouse oferece suporte a muitos, mas não todos, os tabela recursos oferecidos por outras bases de dados.  A lista seguinte mostra alguns dos recursos de tabela que não são suportados no SQL Data Warehouse.

- Chave primária, as chaves estrangeiras, recursos exclusivos, verificação de [as restrições da tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [Colunas calculadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Vistas indexadas](/sql/relational-databases/views/create-indexed-views)
- [Sequência](/sql/t-sql/statements/create-sequence-transact-sql)
- [Colunas esparsas](/sql/relational-databases/tables/use-sparse-columns)
- Chaves alternativas. Implementar com [identidade](sql-data-warehouse-tables-identity.md).
- [Sinónimos](/sql/t-sql/statements/create-synonym-transact-sql)
- [Acionadores](/sql/t-sql/statements/create-trigger-transact-sql)
- [Índices exclusivos](/sql/t-sql/statements/create-index-transact-sql)
- [Tipos definidos pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Consultas de tamanho de tabela
Uma forma simples para identificar o espaço e linhas consumidas por uma tabela em cada um dos 60 distribuições, é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, usar comandos DBCC pode ser bastante limitativos.  Vistas de gestão dinâmica (DMVs) mostram mais detalhes daqueles comandos DBCC. Comece por criar esta vista.

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

Esta consulta devolve as linhas e espaço por tabela.  Permite-lhe ver que tabelas são as maiores tabelas e são round robin, replicados, ou hash - distribuído.  Para tabelas de hash e distribuída, a consulta mostra a coluna de distribuição.  

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

## <a name="next-steps"></a>Passos Seguintes
Depois de criar as tabelas para o seu armazém de dados, a próxima etapa é carregar dados para a tabela.  Para obter um tutorial de carregamento, veja [carregamento de dados para o SQL Data Warehouse](load-data-wideworldimportersdw.md).
