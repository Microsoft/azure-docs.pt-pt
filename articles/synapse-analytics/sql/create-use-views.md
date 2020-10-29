---
title: Criar e utilizar vistas em SQL on-demand (pré-visualização)
description: Nesta secção, você vai aprender a criar e usar vistas para embrulhar as consultas SQL on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com o SQL a pedido.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c965b27fde80e1cb8d52b1061917d83e5cd7f5d6
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927809"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Criar e utilizar vistas em SQL on-demand (pré-visualização) usando Azure Synapse Analytics

Nesta secção, você vai aprender a criar e usar vistas para embrulhar as consultas SQL on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com o SQL a pedido.

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
