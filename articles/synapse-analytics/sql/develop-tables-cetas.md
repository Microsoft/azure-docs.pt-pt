---
title: CRIAR TABELA EXTERNA COMO SELECT (CETAS) em Sinaapse SQL
description: Utilizar a tabela externa COMO SELECT (CETAS) com Sinapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677566"
---
# <a name="cetas-with-synapse-sql"></a>CETAS com Sinaapse SQL

Pode utilizar a CRIAÇÃO DE TABELA EXTERNA AS SELECT (CETAS) em piscina SQL dedicada ou piscina SQL sem servidor para completar as seguintes tarefas:  

- Criar uma tabela externa
- Exportar, paralelamente, os resultados de uma declaração seleta Transact-SQL para:

  - Hadoop
  - Azure Storage Blob
  - Armazenamento do Azure Data Lake Ger2

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS em piscina SQL dedicada

Para obter piscina SQL dedicada, utilização cetas e sintaxe, verifique o artigo [CREATE EXTERNAL TABLE AS SELECT.](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) Além disso, para obter orientação sobre ctas utilizando piscina SQL dedicada, consulte o artigo [CREATE TABLE AS SELECT.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)

## <a name="cetas-in-serverless-sql-pool"></a>CETAS na piscina SQL sem servidor

Ao utilizar a piscina SQL sem servidor, o CETAS é utilizado para criar uma tabela externa e resultados de consulta de exportação para a Azure Storage Blob ou Azure Data Lake Storage Gen2.

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

*[database_name  . *[schema_name* ] . ] | *schema_name.* ] *table_name**

O nome de uma a três partes da mesa para criar. Para uma tabela externa, a piscina SQL sem servidor armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado em piscina SQL sem servidor.

LOCALIZAÇÃO = *'path_to_folder'*

Especifica onde escrever os resultados da declaração SELECT na fonte de dados externos. A pasta raiz é a localização de dados especificada na origem de dados externa. A LOCALIZAÇÃO deve apontar para uma pasta e ter um trailing /. Exemplo: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Especifica o nome do objeto de origem de dados externo que contém o local onde os dados externos serão armazenados. Para criar uma fonte de dados externa, utilize [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Especifica o nome do objeto de formato de ficheiro externo que contém o formato do ficheiro de dados externo. Para criar um formato de ficheiro externo, utilize [o FORMATO DE FICHEIRO EXTERNO CREATE (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Apenas os formatos de ficheiros externos com FORMAT_TYPE=PARQUET e FORMAT_TYPE=DELIMITEDTEXT são atualmente suportados. A compressão GZip para o formato DELIMITEDTEXT não é suportada.

COM *<common_table_expression>*

Especifica um conjunto de resultados nomeado temporário, conhecido como uma expressão de mesa comum (CTE). Para obter mais informações, consulte [with common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

SELECIONE <select_criteria>

Povoa a nova tabela com os resultados de uma declaração SELECT. *select_criteria* é o corpo da declaração SELECT que determina quais os dados a copiar para a nova tabela. Para obter informações sobre declarações SELECT, consulte [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> ENCOMENDA POR cláusula em SELECT não é suportado para CETAS.

## <a name="permissions"></a>Permissões

É necessário ter permissões para listar o conteúdo da pasta e escrever na pasta LOCATION para que o CETAS funcione.

## <a name="examples"></a>Exemplos

Estes exemplos utilizam o CETAS para salvar a população total agregada por ano e o estado para uma pasta aggregated_data que está localizada na population_ds fonte de dados.

Esta amostra baseia-se na credencial, fonte de dados e formato de ficheiro externo criado anteriormente. Consulte o documento das [tabelas externas.](develop-tables-external-tables.md) Para guardar os resultados da consulta para uma pasta diferente na mesma fonte de dados, altere o argumento LOCALIZAÇÃO. 

Para guardar resultados para uma conta de armazenamento diferente, crie e utilize uma fonte de dados diferente para DATA_SOURCE argumento.

> [!NOTE]
> As amostras que se seguem utilizam uma conta pública de armazenamento de dados abertos. É só leitura. Para executar estas consultas, precisa fornecer a fonte de dados para a qual tem permissões de escrita.

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

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

A amostra a seguir utiliza uma tabela externa como fonte para o CETAS. Baseia-se na credencial, fonte de dados, formato de ficheiro externo e tabela externa criada anteriormente. Consulte o documento das [tabelas externas.](develop-tables-external-tables.md)

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

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Tipos de dados suportados

O CETAS pode ser utilizado para armazenar conjuntos de resultados com os seguintes tipos de dados SQL:

- binary
- varbinário
- char
- varchar
- nchar
- nvarchar
- pequeno encontro
- data
- datetime
- datetime2
- datatimeoffoff
- hora
- decimal
- numeric
- float
- real
- bigint
- tinyint
- smallint
- int
- bigint
- bit
- dinheiro
- pequeno dinheiro
- uniqueidentifier

> [!NOTE]
> LOBs maiores que 1MB não podem ser usados com CETAS.

## <a name="next-steps"></a>Passos seguintes

Tente consultar o [Apache Spark para as tabelas externas Azure Synapse](develop-storage-files-spark-tables.md).
