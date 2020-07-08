---
title: Ficheiros De consulta Parquet utilizando SQL on demand (pré-visualização)
description: Neste artigo, você aprenderá a consultar ficheiros Parquet usando SQL on demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207571"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ficheiros De consulta Parquet utilizando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on demand (pré-visualização) que irá ler ficheiros Parquet.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** com uma fonte de dados que refira a conta de armazenamento de Táxi Amarelo [NYC.](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="dataset"></a>Conjunto de dados

[O](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) conjunto de dados do Táxi Amarelo nyc é usado nesta amostra. Pode consultar os ficheiros do Parquet da mesma forma que [leu ficheiros CSV.](query-parquet-files.md) A única diferença é que o `FILEFORMAT` parâmetro deve ser definido para `PARQUET` . Exemplos neste artigo mostram as especificidades da leitura dos ficheiros Parquet.

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de arquivos de parquet

Pode especificar apenas as colunas de interesse quando consultar os ficheiros Parquet.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Inferência automática do esquema

Não precisa de utilizar a cláusula OPENROWSET COM A cláusula ao ler ficheiros Parquet. Os nomes das colunas e os tipos de dados são automaticamente lidos a partir de ficheiros Parquet.

A amostra abaixo mostra as capacidades automáticas de inferência de esquema para ficheiros Parquet. Devolve o número de linhas em setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Não é preciso especificar colunas na cláusula OPENROWSET COM A cláusula ao ler ficheiros Parquet. Nesse caso, o serviço de consulta a pedido da SQL utilizará metadados no ficheiro Parquet e ligará colunas pelo nome.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Dados de partição de consultas

O conjunto de dados fornecido nesta amostra é dividido (dividido) em sub-dobradeiras separadas. Pode direcionar divisórias específicas utilizando a função de filepa. Este exemplo mostra os valores das tarifas por ano, mês e payment_type nos primeiros três meses de 2017.

> [!NOTE]
> A consulta a pedido do SQL é compatível com o esquema de partição hive/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Mapeamento de tipo

Os ficheiros parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos de Parquet são mapeados para os tipos nativos SQL.

| Tipo parquet | Tipo lógico parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEANA | | bit |
| BINÁRIO / BYTE_ARRAY | | varbinário |
| DUPLO | | float |
| FLUTUAR | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINÁRIO |UTF8 |varchar \* (colagem UTF8) |
| BINÁRIO |CORDA |varchar \* (colagem UTF8) |
| BINÁRIO |ENUM|varchar \* (colagem UTF8) |
| BINÁRIO |UUID |uniqueidentifier |
| BINÁRIO |DECIMAL |decimal |
| BINÁRIO |JSON |varchar(máx) \* (colagem UTF8) |
| BINÁRIO |Rio BSON |varbinário(máx) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALO |varchar(máx), serializado em formato padronizado |
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
| INT64 |TEMPO (MICROS / NANOS) |hora |
|INT64 |TIMETAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar(máx), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAPA|varchar(máx), serializado em JSON |

## <a name="next-steps"></a>Próximos passos

Avance para o próximo artigo para aprender a [questionar os tipos aninhados de Parquet](query-parquet-nested-types.md).
