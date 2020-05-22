---
title: Criar e utilizar pontos de vista em SQL on-demand (pré-visualização)
description: Nesta secção, você aprenderá a criar e usar vistas para embrulhar consultas sql on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com a SQL a pedido.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: ca60b7c12ec7e7a5e04202e377c345055ce1090c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736012"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Crie e use vistas em SQL on-demand (pré-visualização) usando Azure Synapse Analytics

Nesta secção, você aprenderá a criar e usar vistas para embrulhar consultas sql on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com a SQL a pedido.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é criar uma base de dados onde a vista será criada e inicializar os objetos necessários para autenticar no armazenamento do Azure executando script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Todas as consultas neste artigo serão executadas na sua base de dados de amostras.

## <a name="create-a-view"></a>Criar uma vista

Pode criar vistas da mesma forma que cria vistas regulares do SQL Server. A seguinte consulta cria uma visão que lê o ficheiro *population.csv.*

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou.

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

A visão neste exemplo utiliza `OPENROWSET` a função que usa o caminho absoluto para os ficheiros subjacentes. Se tiver `EXTERNAL DATA SOURCE` um URL raiz do seu armazenamento, pode usar com e relativo caminho de `OPENROWSET` `DATA_SOURCE` ficheiro:

```
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

Pode utilizar visualizações nas suas consultas da mesma forma que utiliza vistas em consultas do SQL Server.

A seguinte consulta demonstra usando a visão *population_csv* que criamos na [Create a view](#create-a-view). Devolve nomes de país/região com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou.

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

Para obter informações sobre como consultar diferentes tipos de ficheiros, consulte o [ficheiro CSV único](query-single-csv-file.md)da [Consulta, ficheiros Query Parquet](query-parquet-files.md)e artigos de [ficheiros Consulta JSON.](query-json-files.md)
