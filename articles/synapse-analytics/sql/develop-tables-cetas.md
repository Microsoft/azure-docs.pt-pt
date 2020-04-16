---
title: CETAS em Synapse SQL
description: Utilização cetas com SqL synapse
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424028"
---
# <a name="cetas-with-synapse-sql"></a>CETAS com Synapse SQL

Em conjunto SQL ou SQL a pedido (pré-visualização), pode utilizar a CREATE EXTERNAL TABLE AS SELECT (CETAS) para completar as seguintes tarefas:  

- Criar uma tabela externa
- Exportar, paralelamente, os resultados de uma declaração da Transact-SQL SELECT para

  - Hadoop
  - Blob de armazenamento azure
  - Armazenamento do Azure Data Lake Ger2

## <a name="cetas-in-sql-pool"></a>CETAS em piscina SQL

Para piscina SQL, utilização cetas e sintaxe, consulte a [tabela EXTERNA CREATE AS SELECT.](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Além disso, para orientação sobre CTAS utilizando piscina SQL, consulte o artigo [CREATE TABLE AS SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="cetas-in-sql-on-demand"></a>CETAS em SQL a pedido

Ao utilizar o recurso SQL on-demand, o CETAS é utilizado para criar uma tabela externa e resultados de consulta de exportação para o Azure Storage Blob ou azure Data Lake Storage Gen2.

## <a name="syntax"></a>Sintaxe

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumentos

*[ *database_name.* *[schema_name]* . ] | *schema_name.* ] *table_name**

O nome de uma a três partes da mesa para criar. Para uma tabela externa, a SQL a pedido armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado em SQL a pedido.

LOCALIZAÇÃO = *'path_to_folder'*

Especifica onde escrever os resultados da declaração SELECT na fonte de dados externos. A pasta raiz é a localização de dados especificada na origem de dados externa. Localização deve apontar para uma pasta e ter um trailing /. Exemplo: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Especifica o nome do objeto de origem de dados externo que contém a localização onde os dados externos serão armazenados. Para criar uma fonte externa de dados, utilize a [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Especifica o nome do objeto de formato de ficheiro externo que contém o formato para o ficheiro de dados externo. Para criar um formato de ficheiro externo, utilize o [FORMATO DE FICHEIRO EXTERNO (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Atualmente, apenas são suportados formatos de ficheiros externos com FORMAT='PARQUET'.

COM *<>common_table_expression*

Especifica um conjunto de resultados nomeado temporário, conhecido como expressão de mesa comum (CTE). Para mais informações, consulte [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

SELECIONe <> select_criteria

Povoa a nova tabela com os resultados de uma declaração SELECT. *select_criteria* é o corpo da declaração SELECT que determina quais os dados a copiar para a nova tabela. Para obter informações sobre as declarações SELECT, consulte [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="permissions"></a>Permissões

É necessário ter permissões para listar o conteúdo das pastas e escrever para a pasta LOCATION para o CETAS funcionar.

## <a name="examples"></a>Exemplos

Estes exemplos usam o CETAS para salvar a população total agregada por ano e afirmam a uma pasta aggregated_data que está localizada na fonte de dados population_ds.

Esta amostra baseia-se na credencial, fonte de dados e formato de ficheiro externo criado anteriormente. Consulte o documento das [tabelas externas.](develop-tables-external-tables.md) Para guardar os resultados da consulta para uma pasta diferente na mesma fonte de dados, altere o argumento DE LOCALIZAÇÃO. Para guardar resultados para uma conta de armazenamento diferente, crie e use uma fonte de dados diferente para DATA_SOURCE argumento.

> [!NOTE]
> As amostras que se seguem utilizam uma conta pública de armazenamento de Dados Abertos Azure. É só para ler. Para executar estas consultas, precisa fornecer a fonte de dados para a qual tem permissões de escrita.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

A amostra abaixo utiliza uma tabela externa como fonte para o CETAS. Baseia-se na credencial, fonte de dados, formato de ficheiro externo e tabela externa criada anteriormente. Consulte o documento das [tabelas externas.](develop-tables-external-tables.md)

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Tipos de dados suportados

O CETAS pode ser utilizado para armazenar conjuntos de resultados com os seguintes tipos de dados SQL:

- binary
- varbinary
- char
- varchar
- date
- hora
- datetime2
- decimal
- numeric
- float
- real
- bigint
- int
- smallint
- tinyint
- bit

Os LOBs não podem ser utilizados com CETAS.

Os seguintes tipos de dados não podem ser utilizados em parte SELECT do CETAS:

- nchar
- nvarchar
- datetime
- tempo de data pequena
- datacompensação
- dinheiro
- dinheiro pequeno
- uniqueidentifier

## <a name="next-steps"></a>Passos seguintes

Pode experimentar as [mesas spark.](develop-storage-files-spark-tables.md)
