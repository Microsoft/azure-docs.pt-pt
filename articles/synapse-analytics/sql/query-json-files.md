---
title: Consulta ficheiros JSON usando piscina SQL sem servidor
description: Esta secção explica como ler ficheiros JSON usando piscina SQL sem servidor em Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5703e4ac3d4a545041c7d258f0b1f0e7c79a33b5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462616"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Consulta ficheiros JSON usando piscina SQL sem servidor em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando a piscina SQL sem servidor em Azure Synapse Analytics. O objetivo da consulta é ler ficheiros JSON utilizando [OPENROWSET](develop-openrowset.md). 
- Ficheiros JSON padrão onde vários documentos JSON são armazenados como uma matriz JSON.
- Ficheiros JSON delimitados em linha, onde os documentos JSON são separados com o carácter de nova linha. As extensões comuns para este tipo de ficheiros são `jsonl` `ldjson` , e `ndjson` .

## <a name="read-json-documents"></a>Leia documentos da JSON

A maneira mais fácil de ver o conteúdo do seu ficheiro JSON é fornecer o URL de ficheiro à `OPENROWSET` função, especificar csv, `FORMAT` e definir valores para `0x0b` e `fieldterminator` . `fieldquote` . Se precisas de ler ficheiros JSON delimitados pela linha, então isto é suficiente. Se tiver um ficheiro JSON clássico, terá de definir valores `0x0b` para `rowterminator` . `OPENROWSET` a função analisará o JSON e devolverá todos os documentos no seguinte formato:

| doc |
| --- |
|{"date_rep":"2020-07-24","dia":24,"mês":7,"ano":2020,"casos":3,"mortes":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-25","dia":25,"mês":7,"ano":2020,"casos":7,"mortes":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-26","dia":26,"mês":7,"ano":2020,"casos":4,"mortes":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-27","dia":27,"mês":7,"ano":2020,"casos":8,"mortes":0,"geo_id":"AF"}|

Se o ficheiro estiver disponível publicamente, ou se a sua identidade AZure AD puder aceder a este ficheiro, deverá ver o conteúdo do ficheiro utilizando a consulta como a apresentada nos seguintes exemplos.

### <a name="read-json-files"></a>Ler ficheiros JSON

A seguinte consulta de amostra lê JSON e ficheiros JSON delimitados em linha, e devolve todos os documentos como uma linha separada.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

O documento JSON na consulta de amostra anterior inclui uma variedade de objetos. A consulta devolve cada objeto como uma linha separada no conjunto de resultados. Certifique-se de que pode aceder a este ficheiro. Se o seu ficheiro estiver protegido com a chave SAS ou identidade personalizada, terá de configurar a [credencial ao nível do servidor para o início de sessão](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Utilização de fontes de dados

Exemplo anterior utiliza o caminho completo para o ficheiro. Como alternativa, pode criar uma fonte de dados externa com a localização que aponta para a pasta raiz do armazenamento, e utilizar essa fonte de dados e o caminho relativo para o ficheiro na `OPENROWSET` função:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, pode configurar [fonte de dados com credencial](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)de área de dados .

Nas seguintes secções, pode ver como consultar vários tipos de ficheiros JSON.

## <a name="parse-json-documents"></a>Documentos da Parse JSON

As consultas nos exemplos anteriores devolvem todos os documentos JSON como uma única corda numa linha separada do conjunto de resultados. Pode utilizar funções `JSON_VALUE` e `OPENJSON` analisar os valores nos documentos JSON e devolvê-los como valores relacionais, como é mostrado no seguinte exemplo:

| representante de data \_ | casos | geo \_ id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Documento JSON da amostra

Os exemplos de consulta lêem ficheiros *json* contendo documentos com a seguinte estrutura:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Se estes documentos forem armazenados como JSON delimitado em linha, tem de definir `FIELDTERMINATOR` e `FIELDQUOTE` 0x0b. Se tiver o formato JSON padrão, tem de definir `ROWTERMINATOR` até 0x0b.

### <a name="query-json-files-using-json_value"></a>Consultar ficheiros JSON usando JSON_VALUE

A consulta abaixo mostra-lhe como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalar (título, editor) a partir de documentos JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Consulta ficheiros JSON usando OPENJSON

A seguinte consulta utiliza [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Recuperará as estatísticas do COVID relatadas na Sérvia:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Passos seguintes

Os próximos artigos desta série demonstrarão como:

- [Consulta de pastas e vários ficheiros](query-folders-multiple-csv-files.md)
- [Criar e utilizar vistas](create-use-views.md)
