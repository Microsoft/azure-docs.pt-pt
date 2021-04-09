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
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225596"
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
> Se estes documentos forem armazenados como JSON delimitado por linha, tem de definir `FIELDTERMINATOR` e `FIELDQUOTE` 0x0b. Se tiver o formato JSON padrão, tem de definir `ROWTERMINATOR` para 0x0b.

### <a name="query-json-files-using-json_value"></a>Consultar ficheiros JSON usando JSON_VALUE

A consulta abaixo mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) para recuperar valores escalares `date_rep` (, `countries_and_territories` ) a partir `cases` de documentos JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
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

Uma vez extraídas propriedades JSON de um documento JSON, pode definir pseudónimos de coluna e lançar opcionalmente o valor textual para algum tipo.

### <a name="query-json-files-using-openjson"></a>Consulta ficheiros JSON usando OPENJSON

A seguinte consulta utiliza [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true). Recuperará as estatísticas do COVID relatadas na Sérvia:

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
Os resultados são funcionalmente os mesmos que os resultados devolvidos usando a `JSON_VALUE` função. Em alguns casos, `OPENJSON` pode ter vantagem `JSON_VALUE` sobre:
- Na `WITH` cláusula pode definir explicitamente os pseudónimos da coluna e os tipos de cada propriedade. Não é preciso colocar a `CAST` função em todas as colunas da `SELECT` lista.
- `OPENJSON` pode ser mais rápido se você estiver devolvendo um grande número de propriedades. Se estiver a devolver apenas 1-2 propriedades, a `OPENJSON` função pode ser sobrecarga.
- Deve utilizar a `OPENJSON` função se precisar de analisar a matriz de cada documento e juná-la à linha dos pais.

## <a name="next-steps"></a>Passos seguintes

Os próximos artigos desta série demonstrarão como:

- [Consulta de pastas e vários ficheiros](query-folders-multiple-csv-files.md)
- [Criar e utilizar vistas](create-use-views.md)
