---
title: Criar e utilizar tabelas externas em SQL a pedido (pré-visualização)
description: Nesta secção, aprenderá a criar e utilizar tabelas externas em SQL on-demand (pré-visualização). As tabelas externas são úteis quando pretende controlar o acesso a dados externos no SQL On-demand e se pretender utilizar ferramentas, como o Power BI, em conjunto com a SQL on-demand.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424553"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Crie e utilize tabelas externas em SQL on-demand (pré-visualização) utilizando o Azure Synapse Analytics

Nesta secção, aprenderá a criar e utilizar tabelas externas em SQL on-demand (pré-visualização). As tabelas externas são úteis quando pretende controlar o acesso a dados externos no SQL On-demand e se pretender utilizar ferramentas, como o Power BI, em conjunto com a SQL on-demand.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é rever os artigos abaixo e certificar-se de que cumpriu os pré-requisitos para criar e utilizar tabelas externas a pedido da SQL:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Criar uma tabela externa

Pode criar tabelas externas da mesma forma que cria tabelas externas regulares do SQL Server. A consulta abaixo cria uma tabela externa que lê o ficheiro *population.csv.*

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Use uma tabela externa

Pode utilizar tabelas externas nas suas consultas da mesma forma que as utiliza em consultas do SQL Server.

A seguinte consulta demonstra o uso da tabela externa *da população* que criamos na [Criação de uma secção de tabela externa.](#create-an-external-table) Devolve nomes de países com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. Se não criou uma base de dados, por favor leia a [configuração pela primeira vez](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como armazenar os resultados de uma consulta ao armazenamento consulte os [resultados](../sql/create-external-table-as-select.md)da consulta da Loja para o armazenamento .
