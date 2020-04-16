---
title: Pastas de consulta e vários ficheiros CSV utilizando sQL on-demand (pré-visualização)
description: O SQL on-demand (pré-visualização) suporta a leitura de vários ficheiros/pastas utilizando wildcards, que são semelhantes aos wildcards utilizados no Windows OS.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431803"
---
# <a name="query-folders-and-multiple-csv-files"></a>Questiões e vários ficheiros CSV  

Neste artigo, você aprenderá a escrever uma consulta usando SQL on-demand (pré-visualização) em Azure Synapse Analytics.

O SQL on-demand suporta a leitura de vários ficheiros/pastas utilizando wildcards, que são semelhantes aos wildcards utilizados no Windows OS. No entanto, existe uma maior flexibilidade, uma vez que são permitidos múltiplos wildcards.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, certifique-se de rever os artigos listados abaixo:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Leia vários ficheiros na pasta

Utilizará a pasta *csv/táxi* para seguir as consultas de amostra. Contém dados da NYC Taxi - Yellow Taxi Trip Records de julho de 2016 a junho de 2018.

Os *ficheiros em csv/táxi* são nomeados após ano e mês:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Cada ficheiro tem a seguinte estrutura:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Leia todos os ficheiros na pasta
    
O exemplo abaixo lê todos os ficheiros de dados do TÁXI Amarelo nyc da pasta *csv/táxi* e devolve o número total de passageiros e passeios por ano. Também mostra o uso de funções agregadas.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os ficheiros acedidos com o único OPENROWSET devem ter a mesma estrutura (isto é, número de colunas e dos seus tipos de dados).

### <a name="read-subset-of-files-in-folder"></a>Ler subconjunto de ficheiros na pasta

O exemplo abaixo lê-se nos ficheiros de dados do Táxi Amarelo de 2017 da pasta *csv/táxi* utilizando um wildcard e devolve o valor total da tarifa por tipo de pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Todos os ficheiros acedidos com o único OPENROWSET devem ter a mesma estrutura (isto é, número de colunas e dos seus tipos de dados).

## <a name="read-folders"></a>Ler pastas

O caminho que fornece para o OPENROWSET também pode ser um caminho para uma pasta. As seguintes secções incluem estes tipos de consulta.

### <a name="read-all-files-from-specific-folder"></a>Leia todos os ficheiros de uma pasta específica

Pode ler todos os ficheiros numa pasta utilizando o wildcard do nível de ficheiro, como mostrado na [pasta](#read-all-files-in-folder). Mas há uma maneira de consultar uma pasta e consumir todos os ficheiros dentro dessa pasta.

Se o caminho fornecido no OPENROWSET apontar para uma pasta, todos os ficheiros dessa pasta serão utilizados como fonte para a sua consulta. A seguinte consulta irá ler todos os ficheiros na pasta *csv/táxi.*

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo um ficheiro chamado *táxi.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os ficheiros acedidos com o único OPENROWSET devem ter a mesma estrutura (isto é, número de colunas e dos seus tipos de dados).

### <a name="read-all-files-from-multiple-folders"></a>Leia todos os ficheiros de várias pastas

É possível ler ficheiros de várias pastas usando um wildcard. A seguinte consulta irá ler todos os ficheiros de todas as pastas localizadas na pasta *csv* que têm nomes a partir de *t* e terminando com *i*.

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo ficheiros denominados *&ast;t i.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os ficheiros acedidos com o único OPENROWSET devem ter a mesma estrutura (isto é, número de colunas e dos seus tipos de dados).

Uma vez que tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler todos os ficheiros na pasta](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Múltiplos wildcards

Você pode usar vários wildcards em diferentes níveis de caminho. Por exemplo, pode enriquecer consultas anteriores para ler ficheiros apenas com dados de 2017, a partir de todas as pastas que os nomes começam com *t* e terminam com *i*.

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo ficheiros denominados *&ast;t i.*
> Há um limite máximo de 10 wildcards por consulta.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os ficheiros acedidos com o único OPENROWSET devem ter a mesma estrutura (isto é, número de colunas e dos seus tipos de dados).

Uma vez que tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler o subconjunto de ficheiros na pasta](#read-subset-of-files-in-folder) e ler todos os [ficheiros a partir de uma pasta específica](#read-all-files-from-specific-folder). Cenários de utilização de wildcard mais complexos estão cobertos em [ficheiros Query Parquet.](query-parquet-files.md)

## <a name="next-steps"></a>Passos seguintes

Mais informações podem ser encontradas no artigo de [ficheiros específicos](query-specific-files.md) da Consulta.
