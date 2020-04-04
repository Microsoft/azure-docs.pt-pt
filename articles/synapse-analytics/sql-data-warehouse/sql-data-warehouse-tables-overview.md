---
title: Tabelas de design
description: Introdução à conceção de tabelas na piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c5964bc944cd50e05d548eb731450a4944e854d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631271"
---
# <a name="design-tables-in-synapse-sql-pool"></a>Mesas de design na piscina Synapse SQL

Este artigo fornece conceitos introdutórios fundamentais para projetar tabelas na piscina SQL.

## <a name="determine-table-category"></a>Determinar a categoria de tabela

Um [esquema estelar](https://en.wikipedia.org/wiki/Star_schema) organiza dados em tabelas de factos e dimensões. Algumas tabelas são usadas para integração ou dados de encenação antes de passar para uma tabela de factos ou dimensões. Ao desenhar uma tabela, decida se os dados da tabela pertencem a um facto, dimensão ou tabela de integração. Esta decisão informa a estrutura e distribuição de tabelas adequadas.

- **Os quadros** de factos contêm dados quantitativos que são geralmente gerados num sistema transacional e depois carregados no pool SQL. Por exemplo, um negócio de retalho gera transações de vendas todos os dias, e depois carrega os dados numa tabela de fatos de piscina SQL para análise.

- **As tabelas de dimensão** contêm dados de atributos que podem mudar, mas geralmente mudam pouco frequentemente. Por exemplo, o nome e endereço de um cliente são armazenados numa tabela de dimensões e atualizados apenas quando o perfil do cliente muda. Para minimizar o tamanho de uma grande tabela de factos, o nome e endereço do cliente não precisam de estar em todas as filas de uma tabela de factos. Em vez disso, a tabela de factos e a tabela de dimensões podem partilhar uma identificação do cliente. Uma consulta pode juntar-se às duas tabelas para associar o perfil e as transações de um cliente.

- **As tabelas** de integração fornecem um local para integrar ou encenar dados. Você pode criar uma tabela de integração como uma tabela regular, uma mesa externa, ou uma mesa temporária. Por exemplo, pode carregar dados para uma tabela de encenação, realizar transformações nos dados em fase e, em seguida, inserir os dados numa tabela de produção.

## <a name="schema-and-table-names"></a>Schema e nomes de mesa

Os schemas são uma boa maneira de mesas de grupo, usadas de forma semelhante, juntas.  Se está a migrar várias bases de dados de uma solução on-prem para piscina SQL, funciona melhor para migrar todos os fatos, dimensões e tabelas de integração para um esquema na piscina SQL.

Por exemplo, você poderia armazenar todas as tabelas na piscina SQL da amostra [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dentro de um esquema chamado wwi. O seguinte código cria um [esquema definido pelo utilizador](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) chamado wwi.

```sql
CREATE SCHEMA wwi;
```

Para mostrar a organização das mesas na piscina SQL, você poderia usar fato, dim, e int como prefixos para os nomes de mesa. A tabela que se segue mostra alguns dos nomes de schema e tabela saqueados para o WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Tipo de tabela | Piscina SQL |
|:-----|:-----|:------|:-----|
| Localidade | Dimensão | wwi. DimCity |
| Encomenda | Fact | wwi. FactOrder |

## <a name="table-persistence"></a>Persistência de mesa

As tabelas armazenam dados permanentemente no Armazenamento Azure, temporariamente no Armazenamento Azure, ou numa loja de dados externa à piscina SQL.

### <a name="regular-table"></a>Tabela regular

Uma tabela regular armazena dados no Armazenamento Azure como parte da piscina SQL. A tabela e os dados persistem independentemente de uma sessão estar aberta.  O exemplo seguinte cria uma tabela regular com duas colunas.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Mesa temporária

Só existe uma tabela temporária durante a sessão. Pode utilizar uma tabela temporária para evitar que outros utilizadores vejam resultados temporários e também para reduzir a necessidade de limpeza.  

As mesas temporárias utilizam o armazenamento local para oferecer um desempenho rápido.  Para mais informações, consulte [tabelas temporárias.](sql-data-warehouse-tables-temporary.md)

### <a name="external-table"></a>Tabela externa

Uma tabela externa aponta para dados localizados na Blob de Armazenamento Azure ou na Azure Data Lake Store. Quando utilizado em conjunto com a declaração CREATE TABLE AS SELECT, selecionando a partir de uma tabela externa importa dados para o pool SQL.

Como tal, as tabelas externas são úteis para o carregamento de dados. Para um tutorial de carregamento, consulte [Use PolyBase para carregar dados do armazenamento de blob Azure](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Tipos de dados

A piscina SQL suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte os tipos de [dados na referência CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) na declaração create TABLE. Para obter orientações sobre a utilização de tipos de dados, consulte [os tipos de dados](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas

Uma característica fundamental da piscina SQL é a forma como pode armazenar e operar em tabelas através de [distribuições.](massively-parallel-processing-mpp-architecture.md#distributions)  O pool SQL suporta três métodos para distribuir dados: robin redondo (padrão), hash e replicado.

### <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash

Uma tabela distribuída por hash distribui linhas com base no valor da coluna de distribuição. Uma tabela distribuída por hash foi projetada para obter um alto desempenho para consultas em grandes mesas. Há vários fatores a ter em conta na escolha de uma coluna de distribuição.

Para mais informações, consulte [a orientação do Design para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tabelas replicadas

Uma tabela replicada tem uma cópia completa da tabela disponível em cada nó computacional. As consultas são rápidas em tabelas replicadas, uma vez que as juntas em tabelas replicadas não requerem movimento de dados. A replicação requer armazenamento extra, no entanto, e não é prático para mesas grandes.

Para obter mais informações, consulte [a orientação do Design para tabelas replicadas](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Mesas de robin redondo

Uma mesa de robin redondo distribui linhas de mesa uniformemente em todas as distribuições. As filas são distribuídas aleatoriamente. Carregar dados numa mesa de rodapé é rápido.  Tenha em mente que as consultas podem exigir mais movimento de dados do que os outros métodos de distribuição.

Para mais informações, consulte [a orientação do Design para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Métodos comuns de distribuição para tabelas

A categoria de tabela determina frequentemente qual a opção a escolher para a distribuição da tabela.

| Categoria de tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Fact           | Utilize a distribuição de haxixe com índice de colunas agrupadas. O desempenho melhora quando duas tabelas de hash são unidas na mesma coluna de distribuição. |
| Dimensão      | Utilize replicado para tabelas mais pequenas. Se as mesas forem demasiado grandes para serem armazenadas em cada nó Compute, utilize a distribuição de haxixe. |
| Teste        | Use o robin redondo para a mesa de preparação. A carga com CTAS é rápida. Uma vez que os dados estão na tabela de preparação, utilize INSERT... SELECIONE para mover os dados para tabelas de produção. |

## <a name="table-partitions"></a>Divisórias de mesa

Uma mesa dividida armazena e realiza operações nas linhas de mesa de acordo com as gamas de dados. Por exemplo, uma mesa pode ser dividida de dia, mês ou ano. Pode melhorar o desempenho da consulta através da eliminação da partição, o que limita uma consulta aos dados dentro de uma divisória. Também pode manter os dados através da troca de divisórias. Uma vez que os dados no SQL Data Warehouse já estão distribuídos, muitas divisórias podem retardar o desempenho da consulta. Para mais informações, consulte [a orientação da partilha](sql-data-warehouse-tables-partition.md).  Ao trocar em divisórias de mesa que não estejam vazias, considere utilizar a opção TRUNCATE_TARGET na sua declaração [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para que os dados existentes sejam truncados. O código abaixo muda nos dados diários transformados no SalesFact sobrepor quaisquer dados existentes.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Índices Columnstore

Por padrão, a piscina SQL armazena uma tabela como um índice de lojas de colunas agrupadas. Esta forma de armazenamento de dados obtém alta compressão de dados e desempenho de consulta em grandes tabelas.  

O índice de loja de colunas agrupado é geralmente a melhor escolha, mas em alguns casos um índice agrupado ou um heap é a estrutura de armazenamento apropriada.  

> [!TIP]
> Uma tabela de heap pode ser especialmente útil para carregar dados transitórios, como uma mesa de preparação que é transformada em uma mesa final.

Para obter uma lista de funcionalidades da columnstore, consulte [o que há de novo para os índices](/sql/relational-databases/indexes/columnstore-indexes-what-s-new)da columnstore . Para melhorar o desempenho do índice da columnstore, consulte maximizar a qualidade do [grupo de remo para os índices](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)da colunastore .

## <a name="statistics"></a>Estatísticas

O optimizador de consultas usa estatísticas de nível de coluna quando cria o plano para executar uma consulta.

Para melhorar o desempenho da consulta, é importante ter estatísticas sobre colunas individuais, especialmente colunas usadas em juntas de consulta. [A criação de estatísticas](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) acontece automaticamente.  

Atualizar estatísticas não acontece automaticamente. Atualizações estatísticas após um número significativo de linhas adicionadas ou alteradas. Por exemplo, atualizar as estatísticas após uma carga. Para mais informações, consulte [a orientação estatística.](sql-data-warehouse-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Chave primária e chave única

A CHAVE PRIMÁRIA só é suportada quando não são utilizadas não agrupadas e não executadas.  É utilizada uma restrição única com não o 'IMPOSTO' não forçado.  Verifique as restrições da tabela de [piscinaS SQL](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Comandos para a criação de mesas

Pode criar uma mesa como uma nova mesa vazia. Também pode criar e povoar uma tabela com os resultados de uma declaração selecionada. Seguem-se os comandos T-SQL para a criação de uma tabela.

| Declaração T-SQL | Descrição |
|:----------------|:------------|
| [CRIAR TABELA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Cria uma mesa vazia definindo todas as colunas e opções da tabela. |
| [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) | Cria uma mesa externa. A definição da mesa é armazenada na piscina SQL. Os dados da tabela são armazenados no armazenamento Azure Blob ou na Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Povoa uma nova tabela com os resultados de uma declaração selecionada. As colunas de tabela e os tipos de dados baseiam-se nos resultados da declaração selecionada. Para importar dados, esta declaração pode selecionar a partir de uma tabela externa. |
| [CRIAR TABELA EXTERNA COMO SELECIONADO](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Cria uma nova tabela externa exportando os resultados de uma declaração selecionada para um local externo.  A localização é o armazenamento Azure Blob ou a Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-sql-pool"></a>Alinhar dados de origem com o pool SQL

As mesas de bilhar SQL são povoadas carregando dados de outra fonte de dados. Para realizar uma carga bem sucedida, o número e os tipos de dados das colunas nos dados de origem devem alinhar-se com a definição de tabela no pool SQL. Conseguir que os dados se alinhem pode ser a parte mais difícil de desenhar as suas tabelas.

Se os dados vierem de várias lojas de dados, você carrega os dados no pool SQL e armazena-os numa tabela de integração. Uma vez que os dados estão na tabela de integração, você pode usar o poder do pool SQL para realizar operações de transformação. Uma vez preparados os dados, pode inseri-lo em tabelas de produção.

## <a name="unsupported-table-features"></a>Características de mesa não suportadas

A piscina SQL suporta muitas, mas não todas, as funcionalidades da tabela oferecidas por outras bases de dados.  A lista que se segue mostra algumas das funcionalidades da tabela que não são suportadas na piscina SQL:

- Chave estrangeira, Verificar [Restrições de Tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colunas Computadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Pontos de vista indexados](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colunas escassas](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Chaves de aluguer. Implementar com [Identidade.](sql-data-warehouse-tables-identity.md)
- [Sinónimos](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Acionadores](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Índices Únicos](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Tipos definidos pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Consultas de tamanho de mesa

Uma forma simples de identificar o espaço e as filas consumidas por uma tabela em cada uma das 60 distribuições, é usar [o DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, a utilização de comandos DBCC pode ser bastante limitativa.  As vistas de gestão dinâmica (DMVs) mostram mais detalhes do que os comandos DBCC. Comece por criar esta vista:

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
WHERE pn.[type] = 'COMPUTE'
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

### <a name="table-space-summary"></a>Resumo do espaço de mesa

Esta consulta devolve as linhas e o espaço por mesa.  Permite-lhe ver quais as mesas que são as suas maiores mesas e se são rodadas, replicadas ou distribuídas por hash.  Para tabelas distribuídas por hash, a consulta mostra a coluna de distribuição.  

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

### <a name="table-space-by-distribution-type"></a>Espaço de mesa por tipo de distribuição

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

### <a name="table-space-by-index-type"></a>Espaço de mesa por tipo de índice

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

Depois de criar as tabelas para o seu pool SQL, o próximo passo é carregar dados na tabela.  Para um tutorial de carregamento, consulte [os dados de carregamento para a piscina SQL](load-data-wideworldimportersdw.md).
