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
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647514"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Armazenar resultados de consulta para armazenamento usando SQL on-demand (pré-visualização) usando Azure Synapse Analytics

Neste artigo, você aprenderá a armazenar resultados de consulta para armazenamento usando SQL On-demand (pré-visualização).

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

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
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

## <a name="use-the-external-table"></a>Use a tabela externa

Pode utilizar a tabela externa criada através do CETAS como uma tabela externa regular.

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

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como consultar diferentes tipos de ficheiros, consulte o [ficheiro CSV único](query-single-csv-file.md)da [Consulta, ficheiros Query Parquet](query-parquet-files.md)e artigos de [ficheiros Consulta JSON.](query-json-files.md)
