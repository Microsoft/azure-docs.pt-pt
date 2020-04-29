---
title: Consultas de ficheiros Parquet utilizando sQL on-demand (pré-visualização)
description: Neste artigo, você vai aprender a consultar ficheiros Parquet usando SQL on-demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431699"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Arquivos De Consulta Parquet usando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on-demand (pré-visualização) que irá ler ficheiros Parquet.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, reveja os seguintes artigos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Conjunto de dados

Pode consultar ficheiros Parquet da mesma forma que lê ficheiros CSV. A única diferença é que o parâmetro FILEFORMAT deve ser definido para PARQUET. Exemplos neste artigo mostram as especificidades da leitura dos ficheiros Parquet.

> [!NOTE]
> Não é preciso especificar colunas na cláusula OPENROWSET WITH ao ler ficheiros de parquet. A SQL a pedido utilizará metadados no ficheiro Parquet e ligará as colunas pelo nome.

Você usará a pasta *parquet/táxi* para as consultas de amostra. Contém dados do NY Taxi - Yellow Taxi Trip Records de julho de 2016. até junho de 2018.

Os dados são divididos por ano e mês e a estrutura da pasta é a seguinte:

- ano=2016
  - mês=6
  - ...
  - mês=12
- ano=2017
  - mês=1
  - ...
  - mês=12
- ano=2018
  - mês=1
  - ...
  - mês=6

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de ficheiros parquet

Só pode especificar as colunas de interesse quando consultar ficheiros Parquet.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Inferência automática do esquema

Não é necessário utilizar a cláusula OPENROWSET WITH ao ler ficheiros Parquet. Os nomes das colunas e os tipos de dados são lidos automaticamente a partir de ficheiros Parquet.

A amostra abaixo mostra as capacidades automáticas de inferência do esquema para ficheiros Parquet. Devolve o número de filas em setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Não é preciso especificar colunas na cláusula OPENROWSET WITH ao ler ficheiros Parquet. Nesse caso, o serviço De consulta a pedido da SQL utilizará metadados no ficheiro Parquet e ligará as colunas pelo nome.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Dados separados de consulta

O conjunto de dados fornecido nesta amostra é dividido (dividido) em subpastas separadas. Pode direcionar divisórias específicas utilizando a função de pathpath. Este exemplo mostra os valores das tarifas por ano, mês e payment_type para os primeiros três meses de 2017.

> [!NOTE]
> A Consulta a pedido da SQL é compatível com o esquema de partição hive/hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mapeamento de tipo

Os ficheiros Parquet contêm descrições de tipo para cada coluna. A tabela seguinte descreve como os tipos de Parquet são mapeados para tipos nativos SQL.

| Tipo parquet | Tipo lógico parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DUPLO | | float |
| BOIA | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINÁRIO |UTF8 |varchar \*(colagem UTF8) |
| BINÁRIO |CORDA |varchar \*(colagem UTF8) |
| BINÁRIO |ENUM|varchar \*(colagem UTF8) |
| BINÁRIO |UUID |uniqueidentifier |
| BINÁRIO |DECIMAL |decimal |
| BINÁRIO |JSON |varchar (max) \*(colagem UTF8) |
| BINÁRIO |BSON |varbinary (máx) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALO |varchar(max), serializado em formato padronizado |
| INT32 |INT(8, verdade) |smallint |
| INT32 |INT(16, verdade) |smallint |
| INT32 |INT(32, verdade) |int |
| INT32 |INT(8, falso) |tinyint |
| INT32 |INT(16, falso) |int |
| INT32 |INT(32, falso) |bigint |
| INT32 |DATA |date |
| INT32 |DECIMAL |decimal |
| INT32 |TEMPO (MILLIS)|hora |
| INT64 |INT(64, verdade) |bigint |
| INT64 |INT(64, falso) |decimal (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS/ NANOS) |hora |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar(max), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPA|varchar(max), serializado em JSON |

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a [consultar os tipos aninhados do Parquet.](query-parquet-nested-types.md)
