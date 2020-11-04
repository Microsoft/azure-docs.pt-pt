---
title: Tipos de parquet aninhados de consulta usando piscina SQL sem servidor (pré-visualização)
description: Neste artigo, você aprenderá a consultar os tipos aninhados do Parquet utilizando a piscina SQL sem servidor (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3463e4dfc423a3f12ce7a42cb0def36574bcb2d3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312006"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Tipos de aninhados de consulta em ficheiros Parquet e JSON utilizando piscina SQL sem servidor (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando a piscina SQL sem servidor (pré-visualização) em Azure Synapse Analytics. A consulta irá ler os tipos aninhados de Parquet.
Os tipos aninhados são estruturas complexas que representam objetos ou matrizes. Os tipos aninhados podem ser armazenados em: 
- [Parquet,](query-parquet-files.md)onde pode ter várias colunas complexas que contêm matrizes e objetos.
- Ficheiros [hierárquicos do JSON,](query-json-files.md)onde pode ler um documento JSON complexo como uma única coluna.
- Coleções DB Azure Cosmos (atualmente em pré-visualização pública fechada), onde cada documento pode conter propriedades complexas aninhadas.

Os formatos de piscina SQL sem servidor são todos os tipos aninhados como objetos e matrizes JSON. Para que possa [extrair ou modificar objetos complexos utilizando funções JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ou [analisar dados JSON utilizando a função OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Aqui está um exemplo de uma consulta que extrai valores de escalão e objeto do ficheiro JSON do [Conjunto de Dados de Investigação Aberta COVID-19,](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) que contém objetos aninhados: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

A `JSON_VALUE` função devolve um valor escalar do campo no caminho especificado. A `JSON_QUERY` função devolve um objeto formatado como JSON do campo no caminho especificado.

> [!IMPORTANT]
> Este exemplo utiliza um ficheiro do Conjunto de Dados de Investigação Aberta COVID-19. [Consulte aqui a licença e a estrutura dos dados.](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)

## <a name="prerequisites"></a>Pré-requisitos

O primeiro passo é criar uma base de dados onde a fonte de dados será criada. Em seguida, rubricará os objetos executando um [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) na base de dados. O script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nas amostras.

## <a name="project-nested-or-repeated-data"></a>Projeto aninhado ou repetido dados

Um ficheiro Parquet pode ter várias colunas com tipos complexos. Os valores destas colunas são formatados como texto JSON e devolvidos como colunas VARCHAR. A seguinte consulta lê o ficheiro structExample.parquet e mostra como ler os valores das colunas aninhadas: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Esta consulta devolve o seguinte resultado. O conteúdo de cada objeto aninhado é devolvido como texto JSON.

| DataStruct    | TimeStruct    | TimetampStruct   | DecimalStruct | Estrutura flutuante |
| --- | --- | --- | --- | --- |
|{"Data":"2009-04-25"}| {"Tempo":"20:51:54.3598000"}|    {"Timestamp":"5501-04-08 12:13:57.482100"}|    {"Decimal":11143412.25350}| {"Float":0.5}|
|{"Data":"1916-04-29"}| {"Tempo":"00:16:04.6778000"}|    {"Timestamp":"1990-06-30 20:50:52.6828000"}|    {"Decimal":1963545.62800}|  {"Float":-2.125}|

A seguinte consulta lê o ficheiro justSimpleArray.parquet. Projeta todas as colunas a partir do ficheiro Parquet, incluindo dados aninhados e repetidos.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Esta consulta devolverá o seguinte resultado:

|SimpleArray|
| --- |
|[11,12,13]|
|[21,22,23]|

## <a name="read-properties-from-nested-object-columns"></a>Ler propriedades de colunas de objetos aninhados

A `JSON_VALUE` função permite-lhe devolver valores de colunas formatadas como texto JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

O resultado é indicado na tabela seguinte:

|título  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Informação Complementar Um eco-epidemiolo... | Julien   | - Figura S1 : Filologenia de... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Ao contrário dos ficheiros JSON, que na maioria dos casos devolvem uma única coluna que contém um objeto JSON complexo, os ficheiros Parquet podem ter várias colunas complexas. Pode ler as propriedades das colunas aninhadas utilizando a `JSON_VALUE` função em cada coluna. `OPENROWSET` permite especificar diretamente os caminhos das propriedades aninhadas numa `WITH` cláusula. Pode definir os caminhos como o nome de uma coluna, ou pode adicionar uma expressão de [caminho JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) após o tipo de coluna.

A seguinte consulta lê o ficheiro structExample.parquet e mostra como superfície elementos de uma coluna aninhada. Há duas maneiras de fazer referência a um valor aninhado:
- Especificando a expressão do caminho do valor aninhado após a especificação do tipo.
- Formando o nome da coluna como um caminho aninhado, utilizando fazer "." para fazer referência aos campos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elementos de acesso a colunas repetidas

A seguinte consulta lê o ficheiro justSimpleArray.parquet e utiliza [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento escalar de dentro de uma coluna repetida, como uma matriz ou mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Aqui está o resultado:

|SimpleArray    | Primeiro Governo  | Segundo Governo | Terceiro Governo |
| --- | --- | --- | --- |
| [11,12,13] | 11   | 12 | 13 |
| [21,22,23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Aceder a sub-objetos de colunas complexas

A seguinte consulta lê o ficheiro mapExample.parquet e utiliza [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento não escalar de dentro de uma coluna repetida, como uma matriz ou mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Também pode referenciar explicitamente as colunas que pretende devolver numa `WITH` cláusula:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A estrutura `MapOfPersons` é devolvida como uma coluna VARCHAR e formatada como uma corda JSON.

## <a name="project-values-from-repeated-columns"></a>Valores do projeto a partir de colunas repetidas

Se tiver uma série de valores escalares (por `[1,2,3]` exemplo) em algumas colunas, pode expandi-los facilmente e juntá-los com a linha principal usando este script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Passos seguintes

O próximo artigo irá mostrar-lhe como [consultar ficheiros JSON](query-json-files.md).
