---
title: A consulta da loja resulta da piscina SQL sem servidor
description: Neste artigo, você vai aprender a armazenar resultados de consulta para armazenamento usando piscina SQL sem servidor.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: dd989d5925da864728e944e84962086c0cfb08ea
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462319"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Armazenar resultados de consulta para armazenamento usando piscina SQL sem servidor em Azure Synapse Analytics

Neste artigo, você vai aprender a armazenar resultados de consulta para armazenamento usando piscina SQL sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são usados para ler dados nestas amostras.

Siga as instruções deste artigo para criar fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados para escrever dados no armazenamento da saída.

## <a name="create-external-table-as-select"></a>Criar tabela externa como selecionado

Pode utilizar a declaração CREATE EXTERNAL TABLE AS SELECT (CETAS) para armazenar os resultados da consulta.

> [!NOTE]
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
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
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou.

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

Para obter mais informações sobre como consultar diferentes tipos de ficheiros, consulte o [ficheiro CSV único,](query-single-csv-file.md) [os ficheiros Query Parquet](query-parquet-files.md)e [os ficheiros de ficheiros De consulta JSON.](query-json-files.md)
