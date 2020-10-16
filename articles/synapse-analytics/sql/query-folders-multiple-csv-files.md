---
title: Pastas de consulta e vários ficheiros utilizando SQL on demand (pré-visualização)
description: SQL on-demand (pré-visualização) suporta a leitura de vários ficheiros/pastas utilizando wildcards, que são semelhantes aos wildcards utilizados no Windows OS.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 54ef116878dee2ed1c351fac3dacdf359abbe574
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288346"
---
# <a name="query-folders-and-multiple-files"></a>Consultar pastas e vários ficheiros  

Neste artigo, você aprenderá a escrever uma consulta usando SQL on demand (preview) em Azure Synapse Analytics.

SQL suporta a leitura de vários ficheiros/pastas utilizando wildcards, que são semelhantes aos wildcards utilizados no Windows OS. No entanto, existe uma maior flexibilidade, uma vez que são permitidos vários wildcards.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

Você usará a pasta *csv/táxi* para seguir as consultas de amostra. Contém dados da NYC Taxi - Yellow Taxi Trip Records de julho de 2016 a junho de 2018. Os *ficheiros em csv/táxi* são nomeados após ano e mês usando o seguinte padrão: yellow_tripdata_ <year> - <month> .csv

## <a name="read-all-files-in-folder"></a>Leia todos os ficheiros na pasta
    
O exemplo abaixo lê todos os ficheiros de dados do Táxi Amarelo nyc da pasta *csv/táxi* e devolve o número total de passageiros e passeios por ano. Também mostra o uso de funções agregadas.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os ficheiros acedidos com o openrowset único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-subset-of-files-in-folder"></a>Leia o subconjunto de ficheiros na pasta

O exemplo abaixo lê-se nos ficheiros de dados do Táxi Amarelo nyc de 2017 da pasta *csv/táxi* usando um wildcard e devolve o valor total da tarifa por tipo de pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Todos os ficheiros acedidos com o openrowset único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

## <a name="read-folders"></a>Ler pastas

O caminho que fornece ao OPENROWSET também pode ser um caminho para uma pasta. As seguintes secções incluem estes tipos de consulta.

### <a name="read-all-files-from-specific-folder"></a>Leia todos os ficheiros de pastas específicas

Pode ler todos os ficheiros numa pasta utilizando o wildcard de nível de ficheiro, tal como mostrado na [leitura de todos os ficheiros na pasta](#read-all-files-in-folder). Mas há uma maneira de consultar uma pasta e consumir todos os ficheiros dentro dessa pasta.

Se o caminho fornecido em OPENROWSET aponta para uma pasta, todos os ficheiros dessa pasta serão utilizados como fonte para a sua consulta. A seguinte consulta irá ler todos os ficheiros na pasta *csv/táxi.*

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo um ficheiro chamado *táxi.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Todos os ficheiros acedidos com o openrowset único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-all-files-from-multiple-folders"></a>Leia todos os ficheiros de várias pastas

É possível ler ficheiros de várias pastas usando um wildcard. A seguinte consulta irá ler todos os ficheiros de todas as *pastas localizadas* na pasta csv que têm nomes a começar por *t* e terminando com *i*.

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo ficheiros denominados *t &ast; i.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Todos os ficheiros acedidos com o openrowset único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Uma vez que tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler todos os ficheiros na pasta](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Vários wildcards

Você pode usar vários wildcards em diferentes níveis de caminho. Por exemplo, pode enriquecer consultas anteriores para ler ficheiros apenas com dados de 2017, a partir de todas as pastas que os nomes começam com *t* e terminam com *i*.

> [!NOTE]
> Note a existência do/no final do caminho na consulta abaixo. Denota uma pasta. Se o /for omitido, a consulta terá como alvo ficheiros denominados *t &ast; i.*
> Há um limite máximo de 10 wildcards por consulta.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Todos os ficheiros acedidos com o openrowset único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Uma vez que tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que ler o [subconjunto de ficheiros na pasta](#read-subset-of-files-in-folder) e [ler todos os ficheiros de uma pasta específica](#read-all-files-from-specific-folder). Cenários de utilização wildcard mais complexos são cobertos por [ficheiros Parquet De consulta.](query-parquet-files.md)

## <a name="next-steps"></a>Passos seguintes

Mais informações podem ser encontradas no artigo [de ficheiros específicos](query-specific-files.md) da Consulta.
