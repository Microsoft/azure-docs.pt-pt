---
title: Consultas de ficheiros Parquet utilizando sQL on-demand (pré-visualização)
description: Neste artigo, você vai aprender a consultar ficheiros Parquet usando SQL on-demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e9731b869b20c7d8cfc3b1e234711c818a2b7422
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744241"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Arquivos De Consulta Parquet usando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on-demand (pré-visualização) que irá ler ficheiros Parquet.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** com uma fonte de dados que refira a conta de armazenamento de táxi amarelo [nyc.](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Em seguida, inicialize os objetos executando o script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="dataset"></a>Conjunto de dados

O conjunto de dados do [Táxi Amarelo de NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) é utilizado nesta amostra. Pode consultar ficheiros Parquet da mesma forma que [lê ficheiros CSV](query-parquet-files.md). A única diferença é que o `FILEFORMAT` parâmetro deve ser definido para `PARQUET` . Exemplos neste artigo mostram as especificidades da leitura dos ficheiros Parquet.

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de ficheiros parquet

Só pode especificar as colunas de interesse quando consultar ficheiros Parquet.

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

Não é necessário utilizar a cláusula OPENROWSET WITH ao ler ficheiros Parquet. Os nomes das colunas e os tipos de dados são lidos automaticamente a partir de ficheiros Parquet.

A amostra abaixo mostra as capacidades automáticas de inferência do esquema para ficheiros Parquet. Devolve o número de filas em setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Não é preciso especificar colunas na cláusula OPENROWSET WITH ao ler ficheiros Parquet. Nesse caso, o serviço De consulta a pedido da SQL utilizará metadados no ficheiro Parquet e ligará as colunas pelo nome.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Dados separados de consulta

O conjunto de dados fornecido nesta amostra é dividido (dividido) em subpastas separadas. Pode direcionar divisórias específicas utilizando a função de pathpath. Este exemplo mostra os valores das tarifas por ano, mês e payment_type para os primeiros três meses de 2017.

> [!NOTE]
> A Consulta a pedido da SQL é compatível com o esquema de partição hive/hadoop.

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
| BINÁRIO |UTF8 |varchar \* (colagem UTF8) |
| BINÁRIO |CORDA |varchar \* (colagem UTF8) |
| BINÁRIO |ENUM|varchar \* (colagem UTF8) |
| BINÁRIO |UUID |uniqueidentifier |
| BINÁRIO |DECIMAL |decimal |
| BINÁRIO |JSON |varchar (max) \* (colagem UTF8) |
| BINÁRIO |BSON |varbinary (máx) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVALO |varchar(max), serializado em formato padronizado |
| INT32 |INT(8, verdade) |smallint |
| INT32 |INT(16, verdade) |smallint |
| INT32 |INT(32, verdade) |int |
| INT32 |INT(8, falso) |tinyint |
| INT32 |INT(16, falso) |int |
| INT32 |INT(32, falso) |bigint |
| INT32 |DATA |data |
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
