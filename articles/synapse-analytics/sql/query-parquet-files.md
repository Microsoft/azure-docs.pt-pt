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
ms.reviewer: jrasnick
ms.openlocfilehash: 2e00ecd6048239683951a2d1e60d3bcb0eb5aa68
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242437"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ficheiros De consulta Parquet utilizando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on demand (pré-visualização) que irá ler ficheiros Parquet.

## <a name="quickstart-example"></a>Exemplo de arranque rápido

`OPENROWSET` função permite-lhe ler o conteúdo do ficheiro parquet fornecendo o URL ao seu ficheiro.

### <a name="read-parquet-file"></a>Leia o arquivo parquet

A maneira mais fácil de ver o conteúdo do seu ficheiro é fornecer URL de `PARQUET` ficheiro para funcionar e `OPENROWSET` especificar o parquet `FORMAT` . Se o ficheiro estiver disponível publicamente ou se a sua identidade AZure AD puder aceder a este ficheiro, deverá poder ver o conteúdo do ficheiro utilizando a consulta como a apresentada no seguinte exemplo:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Certifique-se de que acede a este ficheiro. Se o seu ficheiro estiver protegido com a chave SAS ou identidade Azure personalizada, terá de configurar a [credencial de nível do servidor para iniciar sessão de sessão](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

### <a name="data-source-usage"></a>Utilização de fontes de dados

Exemplo anterior utiliza o caminho completo para o ficheiro. Como alternativa, pode criar uma fonte de dados externa com a localização que aponta para a pasta raiz do armazenamento, e utilizar essa fonte de dados e o caminho relativo para o ficheiro em `OPENROWSET` função:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, pode configurar [a fonte de dados com credencial](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)de área de dados .

### <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

`OPENROWSET` permite especificar explicitamente quais as colunas que pretende ler a partir do ficheiro usando a `WITH` cláusula:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

Nas secções seguintes pode ver como consultar vários tipos de ficheiros PARQUET.

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

Para o mapeamento do tipo Parquet para mapeamento do tipo nativo [SQL, verifique o tipo de mapeamento para Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a [questionar os tipos aninhados de Parquet](query-parquet-nested-types.md).
