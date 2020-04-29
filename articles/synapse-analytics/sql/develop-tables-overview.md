---
title: Mesas de design usando Synapse SQL
description: Introdução à conceção de tabelas em Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431881"
---
# <a name="design-tables-using-synapse-sql"></a>Mesas de design usando Synapse SQL

Este documento inclui conceitos-chave para a conceção de tabelas com piscina SQL e SQL on-demand (pré-visualização).  

[SQL on-demand (pré-visualização)](on-demand-workspace-overview.md) é um serviço de consulta sobre os dados no seu lago de dados. Não tem armazenamento local para ingestão de dados. [A piscina SQL](best-practices-sql-pool.md) representa uma coleção de recursos analíticos que estão a ser provisionados ao utilizar o SYnapse SQL. O tamanho da piscina SQL é determinado por Unidades de Armazenamento de Dados (DWU).

A tabela que se segue lista os tópicos relevantes para o pool SQL vs. SQL a pedido:

| Tópico                                                        | Conjunto de SQL | SQL a pedido |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Determinar a categoria de tabela](#determine-table-category)        | Sim                | Não                      |
| [Nomes de esquemas](#schema-names)                                | Sim                | Sim                     |
| [Nomes de mesa](#table-names)                                  | Sim                | Não                      |
| [Persistência de mesa](#table-persistence)                      | Sim                | Não                      |
| [Tabela regular](#regular-table)                              | Sim                | Não                      |
| [Mesa temporária](#temporary-table)                          | Sim                | Sim                     |
| [Tabela externa](#external-table)                            | Sim                | Sim                     |
| [Tipos de dados](#data-types)                                    | Sim                | Sim                     |
| [Tabelas distribuídas](#distributed-tables)                    | Sim                | Não                      |
| [Tabelas distribuídas com hash](#hash-distributed-tables)          | Sim                | Não                      |
| [Tabelas replicadas](#replicated-tables)                      | Sim                | Não                      |
| [Mesas de robin redondo](#round-robin-tables)                    | Sim                | Não                      |
| [Métodos comuns de distribuição para tabelas](#common-distribution-methods-for-tables) | Sim                | Não                      |
| [Partições](#partitions)                                    | Sim                | Sim                     |
| [Índices Columnstore](#columnstore-indexes)                  | Sim                | Não                      |
| [Estatísticas](#statistics)                                    | Sim                | Sim                     |
| [Chave primária e chave única](#primary-key-and-unique-key)    | Sim                | Não                      |
| [Comandos para a criação de mesas](#commands-for-creating-tables) | Sim                | Não                      |
| [Alinhar dados de origem com o armazém de dados](#aligning-source-data-with-the-data-warehouse) | Sim                | Não                      |
| [Características de mesa não suportadas](#unsupported-table-features)    | Sim                | Não                      |
| [Consultas de tamanho de mesa](#table-size-queries)                    | Sim                | Não                      |

## <a name="determine-table-category"></a>Determinar a categoria de tabela

Um [esquema estelar](https://en.wikipedia.org/wiki/Star_schema) organiza dados em tabelas de factos e dimensões. Algumas tabelas são usadas para integração ou dados de encenação antes de passar para uma tabela de factos ou dimensões. Ao desenhar uma tabela, decida se os dados da tabela pertencem a um facto, dimensão ou tabela de integração. Esta decisão informa a estrutura e distribuição de tabelas adequadas.

- **Os quadros** de factos contêm dados quantitativos que são geralmente gerados num sistema transacional e depois carregados no armazém de dados. Por exemplo, uma empresa de retalho gera transações de vendas todos os dias, e depois coloca os dados numa tabela de dados para análise.

- **As tabelas de dimensão** contêm dados de atributos que podem mudar, mas geralmente mudam pouco frequentemente. Por exemplo, o nome e endereço de um cliente são armazenados numa tabela de dimensões e atualizados apenas quando o perfil do cliente muda. Para minimizar o tamanho de uma grande tabela de factos, o nome e endereço do cliente não precisam de estar em todas as filas de uma tabela de factos. Em vez disso, a tabela de factos e a tabela de dimensões podem partilhar uma identificação do cliente. Uma consulta pode juntar-se às duas tabelas para associar o perfil e as transações de um cliente.

- **As tabelas** de integração fornecem um local para integrar ou encenar dados. Você pode criar uma tabela de integração como uma tabela regular, uma mesa externa, ou uma mesa temporária. Por exemplo, pode carregar dados para uma tabela de encenação, realizar transformações nos dados em fase e, em seguida, inserir os dados numa tabela de produção.

## <a name="schema-names"></a>Nomes de esquemas

Os schemas são uma boa maneira de agrupar objetos que são usados de forma semelhante. O seguinte código cria um [esquema definido pelo utilizador](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) chamado wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Nomes de mesa

Se você está migrando várias bases de dados de uma solução on-prem para piscina SQL, a melhor prática é migrar todos os fatos, dimensões e tabelas de integração para um esquema de piscina SQL. Por exemplo, você poderia armazenar todas as tabelas no armazém de dados de [amostras WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dentro de um esquema chamado wwi.

Para mostrar a organização das mesas na piscina SQL, você poderia usar fato, dim, e int como prefixos para os nomes de mesa. A tabela abaixo mostra alguns dos nomes de schema e tabela saqueados para O WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Tipo de tabela | Conjunto de SQL |
|:-----|:-----|:------|:-----|
| Localidade | Dimensão | wwi. DimCity |
| Encomenda | Fact | wwi. FactOrder |

## <a name="table-persistence"></a>Persistência de mesa

As tabelas armazenam dados permanentemente no Armazenamento Azure, temporariamente no Armazenamento Azure, ou numa loja de dados externa ao armazém de dados.

### <a name="regular-table"></a>Tabela regular

Uma tabela regular armazena dados no Armazenamento Azure como parte do armazém de dados. A tabela e os dados persistem se uma sessão está ou não aberta.  O exemplo abaixo cria uma tabela regular com duas colunas.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Mesa temporária

Só existe uma tabela temporária durante a sessão. Pode utilizar uma tabela temporária para evitar que outros utilizadores vejam resultados temporários. A utilização de tabelas temporárias também reduz a necessidade de limpeza.  As mesas temporárias utilizam o armazenamento local e, na piscina SQL, podem oferecer um desempenho mais rápido.  

A SQL a pedido suporta mesas temporárias. Mas o seu uso é limitado, uma vez que pode selecionar a partir de uma mesa temporária, mas não pode juntá-lo com ficheiros armazenados.

Para mais informações, consulte [tabelas temporárias.](develop-tables-temporary.md)

### <a name="external-table"></a>Tabela externa

[As tabelas externas](develop-tables-external-tables.md) apontam para dados localizados em Blob de Armazenamento Azure ou no Armazenamento do Lago De Dados Azure.

Importar dados de tabelas externas para o pool SQL utilizando a declaração [CREATE TABLE AS SELECT.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Para um tutorial de carregamento, consulte [Use PolyBase para carregar dados do armazenamento de blob Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para a SQL a pedido, pode utilizar o [CETAS](develop-tables-cetas.md) para guardar o resultado da consulta para uma tabela externa no Armazenamento Azure.

## <a name="data-types"></a>Tipos de dados

A piscina SQL suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte os tipos de [dados na referência CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) na declaração create TABLE. Para obter mais informações sobre a utilização de tipos de dados, consulte [os tipos de dados](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas

Uma característica fundamental da piscina SQL é a forma como pode armazenar e operar em tabelas através de [distribuições.](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)  O pool SQL suporta três métodos para a distribuição de dados:

- Round robin (predefinição)
- Hash
- Replicada

### <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash

Uma tabela distribuída por hash distribui linhas com base no valor da coluna de distribuição. Uma tabela distribuída por hash foi projetada para obter um alto desempenho para consultas em grandes mesas. Há vários fatores a ter em conta na escolha de uma coluna de distribuição.

Para mais informações, consulte [a orientação do Design para tabelas distribuídas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Tabelas replicadas

Uma tabela replicada tem uma cópia completa da tabela disponível em cada nó computacional. As consultas são rápidas em tabelas replicadas porque as juntas em tabelas replicadas não requerem movimento de dados. A replicação requer armazenamento extra, no entanto, e não é prático para mesas grandes.

Para obter mais informações, consulte [a orientação do Design para tabelas replicadas](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Mesas de robin redondo

Uma mesa de robin redondo distribui linhas de mesa uniformemente em todas as distribuições. As filas são distribuídas aleatoriamente. Carregar dados numa mesa de rodapé é rápido.  Mas, as consultas podem exigir mais movimento de dados do que os outros métodos de distribuição.

Para mais informações, consulte [a orientação do Design para tabelas distribuídas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Métodos comuns de distribuição para tabelas

A categoria de tabela determina frequentemente a opção ideal para a distribuição de tabelas.

| Categoria de tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Fact           | Utilize a distribuição de haxixe com índice de colunas agrupadas. O desempenho melhora quando duas tabelas de hash são unidas na mesma coluna de distribuição. |
| Dimensão      | Utilize replicado para tabelas mais pequenas. Se as mesas forem demasiado grandes para serem armazenadas em cada nó Compute, utilize a distribuição de haxixe. |
| Teste        | Use o robin redondo para a mesa de preparação. A carga com CTAS é rápida. Uma vez que os dados estão na tabela de preparação, utilize INSERT... SELECIONE para mover os dados para tabelas de produção. |

## <a name="partitions"></a>Partições

Na piscina SQL, uma mesa dividida armazena e executa operações nas linhas de mesa de acordo com as gamas de dados. Por exemplo, uma mesa pode ser dividida de dia, mês ou ano. Pode melhorar o desempenho da consulta através da eliminação da partição, o que limita uma consulta aos dados dentro de uma divisória.

Também pode manter os dados através da troca de divisórias. Uma vez que os dados no pool SQL já estão distribuídos, muitas divisórias podem retardar o desempenho da consulta. Para mais informações, consulte [a orientação da partilha](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Ao trocar em divisórias de mesa que não estejam vazias, considere utilizar a opção TRUNCATE_TARGET na sua declaração [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para que os dados existentes sejam truncados.

O código abaixo comuta os dados diários transformados numa partilha salesFact e substitui quaisquer dados existentes.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

No SQL a pedido, pode limitar os ficheiros/pastas (divisórias) que serão lidos pela sua consulta. A partilha por caminho é suportada utilizando as funções de filepath e fileinfo descritas nos [ficheiros](develop-storage-files-overview.md)de armazenamento de consulta . O exemplo seguinte lê uma pasta com dados para o ano de 2017:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Índices Columnstore

Por padrão, a piscina SQL armazena uma tabela como um índice de lojas de colunas agrupadas. Esta forma de armazenamento de dados obtém alta compressão de dados e desempenho de consulta em grandes tabelas.  O índice de loja de colunas agrupado é geralmente a melhor escolha, mas em alguns casos um índice agrupado ou um heap é a estrutura de armazenamento apropriada.  

> [!TIP]
> Uma tabela de heap pode ser especialmente útil para carregar dados transitórios, como uma mesa de preparação, que é transformada em uma mesa final.

Para obter uma lista de funcionalidades da columnstore, consulte [o que há de novo para os índices](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)da columnstore . Para melhorar o desempenho do índice da columnstore, consulte maximizar a qualidade do [grupo de remo para os índices](../sql/data-load-columnstore-compression.md)da colunastore .

## <a name="statistics"></a>Estatísticas


O optimizador de consultas usa estatísticas de nível de coluna quando cria o plano para executar uma consulta. Para melhorar o desempenho da consulta, é importante ter estatísticas sobre colunas individuais, especialmente colunas usadas em juntas de consulta. Synapse SQL suporta criação automática de estatísticas. 

A atualização estatística não acontece automaticamente. Atualizações estatísticas após um número significativo de linhas adicionadas ou alteradas. Por exemplo, atualizar as estatísticas após uma carga. Informações adicionais são fornecidas no artigo de [orientação estatística.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Chave primária e chave única

A CHAVE PRIMÁRIA só é suportada quando não são utilizadas não agrupadas e não executadas.  A restrição única só é suportada quando não é utilizada uma restrição única.  Para mais informações, consulte o artigo [SQL Pool Table Constraints.](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="commands-for-creating-tables"></a>Comandos para a criação de mesas

Pode criar uma mesa como uma nova mesa vazia. Também pode criar e povoar uma tabela com os resultados de uma declaração selecionada. Seguem-se os comandos T-SQL para a criação de uma tabela.

| Declaração T-SQL | Descrição |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Cria uma mesa vazia definindo todas as colunas e opções da tabela. |
| [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Cria uma mesa externa. A definição da mesa é armazenada na piscina SQL. Os dados da tabela são armazenados no armazenamento azure Blob ou no Armazenamento do Lago Azure Data. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Povoa uma nova tabela com os resultados de uma declaração selecionada. As colunas de tabela e os tipos de dados baseiam-se nos resultados da declaração selecionada. Para importar dados, esta declaração pode selecionar a partir de uma tabela externa. |
| [CRIAR TABELA EXTERNA COMO SELECIONADO](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Cria uma nova tabela externa exportando os resultados de uma declaração selecionada para um local externo.  A localização é o armazenamento Azure Blob ou o Armazenamento do Lago Azure Data. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Alinhar dados de origem com o armazém de dados

As tabelas de armazéns de dados são povoadas carregando dados de outra fonte de dados. Para obter uma carga bem sucedida, o número e os tipos de dados das colunas nos dados de origem devem alinhar-se com a definição de tabela no armazém de dados.

> [!NOTE]
> Conseguir que os dados se alinhem pode ser a parte mais difícil de desenhar as suas tabelas.

Se os dados vierem de várias lojas de dados, pode deportar os dados para o armazém de dados e armazená-lo numa tabela de integração. Uma vez que os dados estão na tabela de integração, você pode usar o poder do pool SQL para implementar operações de transformação. Uma vez preparados os dados, pode inseri-lo em tabelas de produção.

## <a name="unsupported-table-features"></a>Características de mesa não suportadas

A piscina SQL suporta muitas, mas não todas, as funcionalidades da tabela oferecidas por outras bases de dados.  A lista que se segue mostra algumas das funcionalidades da tabela que não são suportadas na piscina SQL.

- Chave estrangeira, verifique [restrições de tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colunas Computadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Pontos de vista indexados](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Colunas escassas](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Chaves de substituição, implemente com [identidade](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sinónimos](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Acionadores](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Índices Únicos](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Tipos definidos pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Consultas de tamanho de mesa

Uma forma simples de identificar o espaço e as filas consumidas por uma tabela em cada uma das 60 distribuições é utilizar o [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tenha em mente que usar comandos DBCC pode ser bastante limitador.  As vistas de gestão dinâmica (DMVs) mostram mais detalhes do que os comandos DBCC. Comece por criar a vista abaixo.

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

Esta consulta devolve as linhas e o espaço por mesa.  O resumo do espaço de mesa permite-lhe ver quais as mesas que são as suas maiores mesas. Também verá se são redondos, replicados ou distribuídos por hash.  Para tabelas distribuídas por hash, a consulta mostra a coluna de distribuição.  

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

Depois de criar as tabelas para o seu armazém de dados, o próximo passo é carregar dados na tabela.  Para um tutorial de carregamento, consulte [o carregamento de dados na piscina SQL](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).
