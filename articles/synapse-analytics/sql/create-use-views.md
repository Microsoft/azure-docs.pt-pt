---
title: Criar e utilizar pontos de vista em SQL on-demand (pré-visualização)
description: Nesta secção, você aprenderá a criar e usar vistas para embrulhar consultas sql on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com a SQL a pedido.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424539"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Crie e use vistas em SQL on-demand (pré-visualização) usando Azure Synapse Analytics

Nesta secção, você aprenderá a criar e usar vistas para embrulhar consultas sql on-demand (pré-visualização). As vistas permitir-lhe-ão reutilizar essas consultas. Também são necessárias vistas se quiser utilizar ferramentas, como o Power BI, em conjunto com a SQL a pedido.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é rever os artigos abaixo e certificar-se de que cumpriu os pré-requisitos para criar e utilizar pontos de vista on-demand da SQL:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Criar uma vista

Pode criar vistas da mesma forma que cria vistas regulares do SQL Server. A consulta abaixo cria uma visão que lê o ficheiro *population.csv.*

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="use-a-view"></a>Use uma vista

Pode utilizar visualizações nas suas consultas da mesma forma que utiliza vistas em consultas do SQL Server.

A seguinte consulta demonstra usando a visão *population_csv* que criamos na [Create a view](#create-a-view). Devolve nomes de países com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup).

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
