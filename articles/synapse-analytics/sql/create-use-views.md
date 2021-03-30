---
title: Criar e utilizar vistas na piscina SQL sem servidor
description: Nesta secção, você vai aprender a criar e usar vistas para embrulhar consultas de piscina SQL sem servidor. As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se pretender utilizar ferramentas, como o Power BI, em conjunto com a piscina SQL sem servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462279"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Criar e utilizar vistas usando piscina SQL sem servidor em Azure Synapse Analytics

Nesta secção, você vai aprender a criar e usar vistas para embrulhar consultas de piscina SQL sem servidor. As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se pretender utilizar ferramentas, como o Power BI, em conjunto com a piscina SQL sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é criar uma base de dados onde a vista será criada e inicializar os objetos necessários para autenticar no armazenamento do Azure executando [scripts de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Todas as consultas neste artigo serão executadas na sua base de dados de amostras.

## <a name="create-a-view"></a>Criar uma vista

Pode criar vistas da mesma forma que cria vistas regulares do SQL Server. A seguinte consulta cria uma vista que lê *population.csv* ficheiro.

> [!NOTE]
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

A vista neste exemplo usa `OPENROWSET` a função que usa o caminho absoluto para os ficheiros subjacentes. Se tiver `EXTERNAL DATA SOURCE` um URL raiz do seu armazenamento, pode utilizar com e relativo caminho de `OPENROWSET` `DATA_SOURCE` arquivo:

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Use uma vista

Pode utilizar vistas nas suas consultas da mesma forma que utiliza vistas em consultas sql Server.

A seguinte consulta demonstra a utilização da vista *population_csv* que criamos na [Criação de uma vista.](#create-a-view) Devolve nomes de país/região com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como consultar diferentes tipos de ficheiros, consulte o [ficheiro CSV único](query-single-csv-file.md)de consulta, os [ficheiros Query Parquet](query-parquet-files.md)e [os ficheiros de ficheiros De consulta JSON.](query-json-files.md)
