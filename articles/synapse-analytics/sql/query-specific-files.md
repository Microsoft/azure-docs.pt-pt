---
title: Utilização de metadados de ficheiros em consultas
description: A função OPENROWSET fornece informações sobre ficheiros e caminhos sobre todos os ficheiros utilizados na consulta para filtrar ou analisar dados com base no nome do ficheiro e/ou no caminho da pasta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3b4755d1d2e14b8ce3b05cfef6d30d7f6102905d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318821"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Use metadados de ficheiros em consultas de piscina SQL sem servidor

O pool SQL sem servidor pode endereçar vários ficheiros e pastas conforme descrito nas pastas de consulta e no artigo [de vários ficheiros.](query-folders-multiple-csv-files.md) Neste artigo, aprende a utilizar informações de metadados sobre ficheiros e nomes de pastas nas consultas.

Por vezes, poderá ser necessário saber qual o ficheiro ou fonte de pasta que se relaciona com uma linha específica no conjunto de resultados.

Pode utilizar a função `filepath` e `filename` devolver nomes de ficheiros e/ou o caminho no conjunto de resultados. Ou pode usá-los para filtrar dados com base no nome do ficheiro e/ou no caminho da pasta. Estas funções são descritas na [função](query-data-storage.md#filename-function) de nome de ficheiro da secção sintaxe e [função de filepa .](query-data-storage.md#filepath-function) Nas secções seguintes, encontrará pequenas descrições ao longo de amostras.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** com uma fonte de dados que refira a conta de armazenamento. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="functions"></a>Funções

### <a name="filename"></a>Nome de arquivo

Esta função devolve o nome do ficheiro de que a linha é originária.

A amostra a seguir lê os ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017 e devolve o número de passeios por ficheiro. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

O exemplo a seguir mostra como o *nome de ficheiros* pode ser usado na cláusula WHERE para filtrar os ficheiros a serem lidos. Acede a toda a pasta na parte OPENROWSET da consulta e filtra ficheiros na cláusula WHERE.

Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

A função filepath devolve um caminho completo ou parcial:

- Quando chamado sem parâmetro, devolve o caminho completo do ficheiro de onde a linha se origina.
- Quando chamado com um parâmetro, retorna parte do caminho que corresponde ao wildcard na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 devolveria parte do caminho que corresponde ao primeiro wildcard.

A amostra a seguir lê os ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017. Devolve o número de passeios por caminho de arquivo. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

O exemplo a seguir mostra como *o filepath pode* ser usado na cláusula WHERE para filtrar os ficheiros a serem lidos.

Pode utilizar os wildcards na parte OPENROWSET da consulta e filtrar os ficheiros na cláusula WHERE. Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Passos seguintes

No próximo artigo, aprenderá a [consultar ficheiros Parquet.](query-parquet-files.md)
