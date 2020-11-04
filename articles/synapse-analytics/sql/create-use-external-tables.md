---
title: Criar e utilizar tabelas externas na piscina SQL sem servidor (pré-visualização)
description: Nesta secção, você aprenderá a criar e usar tabelas externas na piscina SQL sem servidor (pré-visualização).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 0a52db131311d9956cf7217d910f22c3a4f07738
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314719"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Criar e utilizar tabelas externas utilizando piscina SQL sem servidor (pré-visualização) em Azure Synapse Analytics

Nesta secção, você aprenderá a criar e usar [tabelas externas](develop-tables-external-tables.md) na piscina SQL sem servidor (pré-visualização). As tabelas externas são úteis quando pretende controlar o acesso a dados externos na piscina SQL sem servidor e se pretender utilizar ferramentas, como o Power BI, em conjunto com o pool SQL sem servidor. As tabelas externas podem aceder a dois tipos de armazenamento:
- Armazenamento público onde os utilizadores acedem a ficheiros de armazenamento público.
- Armazenamento protegido onde os utilizadores acedem a ficheiros de armazenamento usando a credencial SAS, identidade AD AZure ou Identidade Gerida do espaço de trabalho Synapse.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é criar uma base de dados onde as tabelas serão criadas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará os seguintes objetos que são usados nesta amostra:
- CREDENCIAL DE ÁREA DE `sqlondemand` DADOS QUE permite o acesso à conta de armazenamento Azure protegida pela `https://sqlondemandstorage.blob.core.windows.net` SAS.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- FONTE DE `sqlondemanddemo` DADOS EXTERNOS que faz referência à conta de armazenamento de demonstração protegida com a chave SAS, e FONTE DE DADOS EXTERNA `YellowTaxi` que faz referência à conta de armazenamento Azure disponível publicamente no local `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` .

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Formatos de `QuotedCSVWithHeaderFormat` ficheiros e `ParquetFormat` que descrevem os tipos de ficheiros CSV e parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

As consultas neste artigo serão executadas na base de dados da amostra e utilizarão estes objetos. 

## <a name="create-an-external-table-on-protected-data"></a>Criar uma tabela externa sobre dados protegidos

Pode criar tabelas externas que acedam a dados numa conta de armazenamento Azure que permite o acesso aos utilizadores com alguma identidade AD ou chave SAS. Pode criar tabelas externas da mesma forma que cria tabelas externas regulares do SQL Server. 

A seguinte consulta cria uma tabela externa que lê *population.csv* ficheiro da conta de armazenamento Demo Azure da SynapseSQL que é referenciada usando fonte de `sqlondemanddemo` dados e protegida com credencial de âmbito de base de dados chamada `sqlondemand` . 

A origem de dados e a credencial de âmbito de base de dados são criadas no [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Criar uma tabela externa sobre dados públicos

Pode criar tabelas externas que leiam dados a partir dos ficheiros colocados no armazenamento Azure disponível publicamente. Este [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) criará fonte de dados externa pública e definição de formato de ficheiro Parquet que é usada na seguinte consulta:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Use uma tabela externa

Pode utilizar [tabelas externas](develop-tables-external-tables.md) nas suas consultas da mesma forma que as utiliza nas consultas sql Server.

A seguinte consulta demonstra isso utilizando a tabela externa *populacional* que criamos na secção anterior. Devolve nomes de país/região com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, isto é, [mydbname], por isso está a usar a base de dados que criou.

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

Para obter informações sobre como armazenar os resultados de uma consulta ao armazenamento consulte os resultados da consulta da Loja ao artigo [de armazenamento.](../sql/create-external-table-as-select.md)
