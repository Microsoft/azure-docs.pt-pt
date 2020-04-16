---
title: Utilização de metadados de ficheiros em consultas
description: A função OPENROWSET fornece informações de ficheiros e caminhos sobre cada ficheiro utilizado na consulta para filtrar ou analisar dados baseados no nome do ficheiro e/ou no caminho da pasta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431556"
---
# <a name="using-file-metadata-in-queries"></a>Utilização de metadados de ficheiros em consultas

O serviço De consulta a pedido SQL pode endereçar vários ficheiros e pastas conforme descrito nas [pastas Da Query e no artigo de vários ficheiros.](query-folders-multiple-csv-files.md) Neste artigo, aprende-se a utilizar informações de metadados sobre ficheiros e nomes de pastas nas consultas.

Por vezes, pode ser necessário saber qual o ficheiro ou fonte de pasta que se correlaciona a uma linha específica no conjunto de resultados.

Pode utilizar `filepath` a `filename` função e devolver os nomes dos ficheiros e/ou o caminho no conjunto de resultados. Ou pode usá-los para filtrar dados com base no nome do ficheiro e/ou no caminho da pasta. Estas funções são descritas na [função](develop-storage-files-overview.md#filename-function) de nome de ficheiro da secção sintaxe e na [função de pathpath](develop-storage-files-overview.md#filepath-function). Abaixo encontrará pequenas descrições ao longo de amostras.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, reveja os seguintes pré-requisitos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="functions"></a>Funções

### <a name="filename"></a>Nome de ficheiro

Esta função devolve o nome de ficheiro de que a linha é originária.

A amostra seguinte lê os ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017 e devolve o número de passeios por ficheiro. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

O exemplo que se segue mostra como o *nome de ficheiro ()* pode ser usado na cláusula WHERE para filtrar os ficheiros a ler. Acede a toda a pasta na parte OPENROWSET da consulta e filtra ficheiros na cláusula WHERE.

Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Arquivo

A função de path derevolta um caminho completo ou parcial:

- Quando chamado sem parâmetro, devolve o caminho de arquivo completo de que a linha provém.
- Quando chamado com um parâmetro, devolve parte do caminho que corresponde ao wildcard na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 devolveria parte do caminho que corresponde ao primeiro wildcard.

A amostra seguinte lê ficheiros de dados do Táxi Amarelo de NYC para os últimos três meses de 2017. Devolve o número de passeios por caminho de arquivo. A parte OPENROWSET da consulta especifica quais os ficheiros que serão lidos.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

O exemplo que se segue mostra como o *processo* pode ser utilizado na cláusula WHERE para filtrar os ficheiros a ler.

Pode utilizar os wildcards na parte OPENROWSET da consulta e filtrar os ficheiros na cláusula WHERE. Os seus resultados serão os mesmos que o exemplo anterior.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
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
