---
title: Tabelas de design usando Synapse SQL
description: Introdução à conceção de tabelas em Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 33eb5977ecb373a0dba87c26cacea247f541be8f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452725"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Tabelas de design usando Synapse SQL em Azure Synapse Analytics

Este documento inclui conceitos-chave para desenhar mesas com piscina SQL dedicada e piscina SQL sem servidor.  

[Serverless SQL pool](on-demand-workspace-overview.md) é um serviço de consulta sobre os dados no seu lago de dados. Não tem armazenamento local para ingestão de dados. [O pool de SQL dedicado](best-practices-sql-pool.md) representa uma coleção de recursos analíticos que estão sendo a provisionados ao utilizar o SQL da Synapse. O tamanho de uma piscina SQL dedicada é determinado por Unidades de Armazenamento de Dados (DWU).

A tabela a seguir lista os tópicos que são relevantes para piscina SQL dedicada vs. piscina SQL sem servidor:

| Tópico                                                        | piscina de SQL dedicada | conjunto de SQL sem servidor |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Determinar categoria de tabela](#determine-table-category)        | Sim                | Não                      |
| [Nomes de schema](#schema-names)                                | Sim                | Sim                     |
| [Nomes de tabelas](#table-names)                                  | Sim                | Não                      |
| [Persistência da tabela](#table-persistence)                      | Sim                | Não                      |
| [Tabela regular](#regular-table)                              | Sim                | Não                      |
| [Tabela temporária](#temporary-table)                          | Sim                | Sim                     |
| [Tabela externa](#external-table)                            | Sim                | Sim                     |
| [Tipos de dados](#data-types)                                    | Sim                | Sim                     |
| [Tabelas distribuídas](#distributed-tables)                    | Sim                | Não                      |
| [Tabelas distribuídas com hash](#hash-distributed-tables)          | Sim                | Não                      |
| [Tabelas replicadas](#replicated-tables)                      | Sim                | Não                      |
| [Mesas de rodapé](#round-robin-tables)                    | Sim                | Não                      |
| [Métodos de distribuição comuns para tabelas](#common-distribution-methods-for-tables) | Sim                | Não                      |
| [Partições](#partitions)                                    | Sim                | Sim                     |
| [Índices Columnstore](#columnstore-indexes)                  | Sim                | Não                      |
| [Estatísticas](#statistics)                                    | Sim                | Sim                     |
| [Chave primária e chave única](#primary-key-and-unique-key)    | Sim                | Não                      |
| [Comandos para criar tabelas](#commands-for-creating-tables) | Sim                | Não                      |
| [Alinhamento dos dados de origem com o armazém de dados](#align-source-data-with-the-data-warehouse) | Sim                | Não                      |
| [Características da tabela não suportadas](#unsupported-table-features)    | Sim                | Não                      |
| [Consultas de tamanho de mesa](#table-size-queries)                    | Sim                | Não                      |

## <a name="determine-table-category"></a>Determinar categoria de tabela

Um [esquema estelar](https://en.wikipedia.org/wiki/Star_schema) organiza dados em tabelas de fatos e dimensões. Algumas tabelas são usadas para integração ou paragem de dados antes de passar para uma tabela de factos ou dimensões. Ao desenhar uma tabela, decida se os dados da tabela pertencem a uma tabela de factos, dimensão ou integração. Esta decisão informa a estrutura e distribuição de mesa adequadas.

- **As tabelas** de factos contêm dados quantitativos que são geralmente gerados num sistema transacional e depois carregados no armazém de dados. Por exemplo, uma empresa de retalho gera transações de vendas todos os dias e, em seguida, carrega os dados numa tabela de factos de armazém de dados para análise.

- **As tabelas de dimensão** contêm dados de atributos que podem mudar, mas geralmente mudam de forma pouco frequente. Por exemplo, o nome e endereço de um cliente são armazenados numa tabela de dimensão e atualizados apenas quando o perfil do cliente muda. Para minimizar o tamanho de uma grande tabela de factos, o nome e endereço do cliente não precisam de estar em todas as filas de uma tabela de factos. Em vez disso, a tabela de factos e a tabela de dimensões podem partilhar uma identificação do cliente. Uma consulta pode juntar-se às duas tabelas para associar o perfil e transações de um cliente.

- **As tabelas de integração** fornecem um local para integrar ou encenar dados. Pode criar uma mesa de integração como mesa regular, uma mesa externa ou uma mesa temporária. Por exemplo, pode carregar dados para uma tabela de encenação, realizar transformações nos dados em encenação e, em seguida, inserir os dados numa tabela de produção.

## <a name="schema-names"></a>Nomes de schema

Os esquemas são uma boa maneira de agrupar objetos que são usados de forma semelhante. O código a seguir cria um [esquema definido pelo utilizador](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) chamado wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Nomes de tabelas

Se você está migrando várias bases de dados de uma solução on-prem para piscina de SQL dedicada, a melhor prática é migrar todas as tabelas de fato, dimensão e integração para um esquema de piscina SQL. Por exemplo, você poderia armazenar todas as tabelas no armazém de dados da amostra [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dentro de um esquema chamado wwi.

Para mostrar a organização das mesas na piscina dedicada SQL, você poderia usar fato, dim, e int como prefixos para os nomes de mesa. A tabela abaixo mostra alguns dos nomes de esquema e tabelas para WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Tipo de mesa | piscina de SQL dedicada |
|:-----|:-----|:------|:-----|
| City | Dimensão | wwi. DimCity |
| Encomenda | Fact | wwi. Ordem dos Factos |

## <a name="table-persistence"></a>Persistência da tabela

As tabelas armazenam dados permanentemente no Azure Storage, temporariamente no Azure Storage, ou numa loja de dados externa ao armazém de dados.

### <a name="regular-table"></a>Tabela regular

Uma tabela regular armazena dados no Azure Storage como parte do armazém de dados. A tabela e os dados persistem se uma sessão está ou não aberta.  O exemplo abaixo cria uma tabela regular com duas colunas.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela temporária

Existe uma tabela temporária apenas durante a duração da sessão. Pode utilizar uma tabela temporária para evitar que outros utilizadores vejam resultados temporários. A utilização de tabelas temporárias também reduz a necessidade de limpeza.  As mesas temporárias utilizam o armazenamento local e, em piscinas SQL dedicadas, podem oferecer um desempenho mais rápido.  

A piscina SQL sem servidor suporta mesas temporárias. Mas, a sua utilização é limitada, uma vez que pode selecionar a partir de uma tabela temporária, mas não pode a junção com ficheiros armazenados.

Para mais informações, consulte [tabelas temporárias.](develop-tables-temporary.md)

### <a name="external-table"></a>Tabela externa

[Tabelas externas](develop-tables-external-tables.md) apontam para dados localizados na bolha de armazenamento Azure ou no Armazenamento do Lago de Dados Azure.

Importar dados de tabelas externas para piscinas SQL dedicadas utilizando a declaração [CREATE TABLE AS SELECT.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Para um tutorial de carregamento, consulte [Use PolyBase para carregar dados do armazenamento de bolhas Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para a piscina SQL sem servidor, pode utilizar [o CETAS](develop-tables-cetas.md) para guardar o resultado da consulta para uma tabela externa no Azure Storage.

## <a name="data-types"></a>Tipos de dados

O pool DE SQL dedicado suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte [os tipos de dados na referência CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes&preserve-view=true) na declaração CREATE TABLE. Para obter mais informações sobre a utilização de tipos de dados, consulte [os tipos de dados](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas

Uma característica fundamental da piscina SQL dedicada é a forma como pode armazenar e operar em mesas através de [distribuições.](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)  O pool de SQL dedicado suporta três métodos para a distribuição de dados:

- Round robin (predefinição)
- Hash
- Replicada

### <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash

Uma tabela distribuída de haxixe distribui linhas com base no valor da coluna de distribuição. Uma tabela distribuída de haxixe é projetada para alcançar um alto desempenho para consultas em mesas grandes. Há vários fatores a ter em conta na escolha de uma coluna de distribuição.

Para obter mais informações, consulte [orientação de Design para tabelas distribuídas.](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="replicated-tables"></a>Tabelas replicadas

Uma tabela replicada tem uma cópia completa da tabela disponível em cada nó computacional. As consultas são rápidas em tabelas replicadas porque as juntas em tabelas replicadas não requerem movimento de dados. A replicação requer armazenamento extra, no entanto, e não é prático para mesas grandes.

Para obter mais informações, consulte [a orientação do Design para tabelas replicadas.](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="round-robin-tables"></a>Mesas de rodapé

Uma mesa de rodapé distribui linhas de mesa uniformemente em todas as distribuições. As linhas são distribuídas aleatoriamente. Carregar dados numa mesa de rodapé é rápido.  Mas, as consultas podem exigir mais movimento de dados do que os outros métodos de distribuição.

Para obter mais informações, consulte [orientação de Design para tabelas distribuídas.](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="common-distribution-methods-for-tables"></a>Métodos de distribuição comuns para tabelas

A categoria de tabela determina frequentemente a opção ideal para a distribuição da tabela.

| Categoria de tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Fact           | Utilize a distribuição de haxixe com índice de loja de colunas agrupados. O desempenho melhora quando duas tabelas de haxixe são unidas na mesma coluna de distribuição. |
| Dimensão      | Utilize replicado para mesas mais pequenas. Se as tabelas forem demasiado grandes para armazenar em cada nó computacional, utilize hash distribuído. |
| Processo de teste        | Use o rodapé para a mesa de preparação. A carga com CTAS é rápida. Uma vez que os dados estão na tabela de preparação, use INSERT... SELECIONE mover os dados para as tabelas de produção. |

## <a name="partitions"></a>Partições

Em piscinas SQL dedicadas, uma mesa dividida armazena e executa operações nas linhas de mesa de acordo com as gamas de dados. Por exemplo, uma mesa pode ser dividida por dia, mês ou ano. Pode melhorar o desempenho da consulta através da eliminação da partição, o que limita uma consulta aos dados dentro de uma partição.

Também pode manter os dados através da comutação de divisórias. Uma vez que os dados de um pool de SQL dedicado já está distribuído, muitas divisórias podem retardar o desempenho da consulta. Para obter mais informações, consulte [a orientação de partição.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)  

> [!TIP]
> Quando a partição mudar para divisórias que não estão vazias, considere utilizar a opção TRUNCATE_TARGET na sua declaração [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) se os dados existentes forem truncados.

O código abaixo comuta os dados diários transformados numa partição SalesFact e substitui quaisquer dados existentes.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

Na piscina SQL sem servidor, pode limitar os ficheiros/pastas (divisórias) que serão lidos pela sua consulta. A partilha por caminho é suportada utilizando as funções de filetim e fileinfo descritas nos [ficheiros de armazenamento de consulta](develop-storage-files-overview.md). O exemplo a seguir lê-se numa pasta com dados relativos ao ano de 2017:

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

Por padrão, a piscina SQL dedicada armazena uma tabela como um índice de loja de colunas agrupado. Esta forma de armazenamento de dados obtém alta compressão de dados e desempenho de consulta em grandes tabelas.  O índice de loja de colunas agrupado é geralmente a melhor escolha, mas em alguns casos um índice agrupado ou uma pilha é a estrutura de armazenamento apropriada.  

> [!TIP]
> Uma tabela de pilhas pode ser especialmente útil para o carregamento de dados transitórios, como uma tabela de encenação, que é transformada em uma mesa final.

Para obter uma lista de funcionalidades de loja de colunas, consulte [as novidades para os índices de loja de colunas.](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Para melhorar o desempenho do índice de loja de colunas, consulte [maximizar a qualidade do grupo de linha para índices de loja de colunas](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Estatísticas

O otimizador de consulta utiliza estatísticas de nível de coluna quando cria o plano para executar uma consulta. Para melhorar o desempenho da consulta, é importante ter estatísticas sobre colunas individuais, especialmente colunas usadas em juntas de consulta. A Synapse SQL suporta a criação automática de estatísticas. 

A atualização estatística não acontece automaticamente. Atualizar as estatísticas após um número significativo de linhas são adicionadas ou alteradas. Por exemplo, atualize as estatísticas após uma carga. Informações adicionais são fornecidas no artigo [de orientação estatística.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Chave primária e chave única

Para piscina SQL dedicada, a CHAVE PRIMÁRIA só é suportada quando não é aplicada e não executada.  A restrição única só é suportada quando não é aplicada.  Para mais informações, consulte o artigo [Restrições de Mesa de Piscina SQL.](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="commands-for-creating-tables"></a>Comandos para criar tabelas

Para piscina SQL dedicada, você pode criar uma mesa como uma nova mesa vazia. Também pode criar e preencher uma tabela com os resultados de uma declaração selecionada. Seguem-se os comandos T-SQL para a criação de uma tabela.

| Declaração T-SQL | Descrição |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma mesa vazia definindo todas as colunas e opções de mesa. |
| [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma mesa externa. A definição da tabela é armazenada em piscina SQL dedicada. Os dados da tabela são armazenados no armazenamento do Azure Blob ou no Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Povoa uma nova tabela com os resultados de uma declaração selecionada. As colunas de tabela e os tipos de dados baseiam-se nos resultados da declaração selecionada. Para importar dados, esta declaração pode selecionar a partir de uma tabela externa. |
| [CRIAR TABELA EXTERNA COMO SELEÇÃO](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma nova tabela externa exportando os resultados de uma declaração selecionada para um local externo.  A localização é o armazenamento de Azure Blob ou Azure Data Lake Storage. |

## <a name="align-source-data-with-the-data-warehouse"></a>Alinhar os dados de origem com o armazém de dados

As mesas de bilhar SQL dedicadas são povoadas carregando dados de outra fonte de dados. Para obter uma carga bem sucedida, o número e os tipos de dados das colunas nos dados de origem devem alinhar-se com a definição de tabela no armazém de dados.

> [!NOTE]
> Conseguir que os dados se alinhem pode ser a parte mais difícil de desenhar as suas tabelas.

Se os dados forem provenientes de várias lojas de dados, pode depositá-lo no armazém de dados e armazená-lo numa tabela de integração. Uma vez que os dados estão na tabela de integração, você pode usar o poder de pool SQL dedicado para implementar operações de transformação. Uma vez preparados os dados, pode inseri-lo em tabelas de produção.

## <a name="unsupported-table-features"></a>Características da tabela não suportadas

O pool dedicado SQL suporta muitas, mas não todas, funcionalidades de tabela oferecidas por outras bases de dados.  A lista que se segue mostra algumas das funcionalidades da tabela que não são suportadas na piscina SQL dedicada.

- Chave estrangeira, verifique [restrições de tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Colunas Computadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Vistas Indexadas](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Colunas Escassas](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- Chaves de substituição, implementar com [identidade](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sinónimos](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Acionadores](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Índices Únicos](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Tipos definidos pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Consultas de tamanho de mesa

Na piscina SQL dedicada, uma forma simples de identificar o espaço e as linhas consumidas por uma mesa em cada uma das 60 distribuições é utilizar o [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tenha em mente que usar comandos DBCC pode ser bastante limitador.  As vistas dinâmicas de gestão (DMVs) mostram mais detalhes do que os comandos DBCC. Comece por criar a vista abaixo.

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

Esta consulta devolve as linhas e o espaço por mesa.  O resumo do espaço de mesa permite-lhe ver quais as mesas que são as suas maiores mesas. Também verá se são rodadas, replicadas ou distribuídas por haxixe.  Para tabelas distribuídas por haxixe, a consulta mostra a coluna de distribuição.  

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

Depois de criar as tabelas para o seu armazém de dados, o próximo passo é carregar dados na tabela.  Para um tutorial de carregamento, consulte [os dados de carregamento na piscina dedicada SQL.](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)
