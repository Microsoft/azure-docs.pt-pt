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
ms.openlocfilehash: f90021e35b4089547b236d01b10820f6c06bd0cc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195169"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Crie e utilize tabelas externas em SQL on-demand (pré-visualização) utilizando o Azure Synapse Analytics

Nesta secção, aprenderá a criar e utilizar tabelas externas em SQL on-demand (pré-visualização). As tabelas externas são úteis quando pretende controlar o acesso a dados externos no SQL On-demand e se pretender utilizar ferramentas, como o Power BI, em conjunto com a SQL on-demand. As tabelas externas podem aceder a dois tipos de armazenamento:
- Armazenamento público onde os utilizadores acedem a ficheiros de armazenamento público.
- Armazenamento protegido onde os utilizadores acedem a ficheiros de armazenamento utilizando a credencial SAS, identidade AD Azure ou Identidade Gerida do espaço de trabalho Synapse.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é criar uma base de dados onde as tabelas serão criadas. Em seguida, inicialize os objetos executando o script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará os seguintes objetos que são utilizados nesta amostra:
- BASE DE DADOS SCOPED CREDENTIAL `sqlondemand` que permite o acesso à conta de armazenamento Azure protegida pela SAS. `https://sqlondemandstorage.blob.core.windows.net`

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- FONTE DE DADOS `sqlondemanddemo` EXTERNOS QUE refere a conta de armazenamento de demonstração protegida com a chave SAS, e FONTE DE DADOS EXTERNOS que faz referência à conta de `YellowTaxi` armazenamento azure disponível publicamente no `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` local.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Formatos de ficheiros e que descrevem tipos de `QuotedCSVWithHeaderFormat` `ParquetFormat` ficheiros CSV e parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

As consultas neste artigo serão executadas na sua base de dados de amostras e utilizarão estes objetos. 

## <a name="create-an-external-table-on-protected-data"></a>Criar uma tabela externa sobre dados protegidos

Pode criar tabelas externas que acedam a dados de uma conta de armazenamento Azure que permita o acesso a utilizadores com alguma identidade Azure AD ou chave SAS. Pode criar tabelas externas da mesma forma que cria tabelas externas regulares do SQL Server. A consulta abaixo cria uma tabela externa que lê o ficheiro *population.csv* da conta de armazenamento de mo Azure synapseSQL que é referenciada usando fonte de dados e protegida com credenciais de base de dados com recurso a base `sqlondemanddemo` de dados chamada `sqlondemand` . A credencial de código de dados e fonte de dados de dados são criadas no script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou. 

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
GO
```

## <a name="create-an-external-table-on-public-data"></a>Criar uma tabela externa sobre dados públicos

Pode criar tabelas externas que lêem dados dos ficheiros colocados no armazenamento Azure disponível ao público. Este script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) criará fonte de dados externas públicas e definição de formato de ficheiro Parquet que é usado na seguinte consulta:

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
## <a name="use-a-external-table"></a>Use uma tabela externa

Pode utilizar tabelas externas nas suas consultas da mesma forma que as utiliza em consultas do SQL Server.

A seguinte consulta demonstra isso usando a tabela externa *da população* que criamos na secção anterior. Devolve nomes de países com a sua população em 2019 por ordem descendente.

> [!NOTE]
> Mude a primeira linha da consulta, ou seja, [mydbname], então está a usar a base de dados que criou.

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

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como armazenar os resultados de uma consulta ao armazenamento consulte os [resultados](../sql/create-external-table-as-select.md)da consulta da Loja para o armazenamento .
