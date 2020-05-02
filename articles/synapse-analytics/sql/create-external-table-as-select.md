---
title: Armazenar resultados de consulta para armazenamento
description: Neste artigo, você aprenderá a armazenar resultados de consulta para armazenamento usando SQL on-demand (pré-visualização).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691869"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Armazenar resultados de consulta para armazenamento usando SQL on-demand (pré-visualização) usando Azure Synapse Analytics

Neste artigo, você aprenderá a armazenar resultados de consulta no armazenamento usando SQL On-demand (pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é rever os artigos abaixo e certificar-se de que cumpriu os pré-requisitos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Criar tabela externa como selecionado

Pode utilizar a declaração CREATE EXTERNAL TABLE AS SELECT (CETAS) para armazenar os resultados da consulta no armazenamento.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup). É necessário alterar o LOCAL para a fonte de dados externa myDataSource para indicar a localização para a qual tem permissão de escrita. 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Use uma tabela externa criada

Pode utilizar uma tabela externa criada através do CETAS como uma tabela externa regular.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como consultar diferentes tipos de ficheiros, consulte o [ficheiro CSV único](query-single-csv-file.md)da [Consulta, ficheiros Query Parquet](query-parquet-files.md)e artigos de [ficheiros Consulta JSON.](query-json-files.md)
