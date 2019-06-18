---
title: Introdução ao catálogo de U-SQL no Azure Data Lake Analytics
description: Saiba como utilizar o catálogo do U-SQL para partilhar código e os dados.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: a6faa7037ccbacc0547401dd52bb3b19abd1c474
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813364"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Introdução ao catálogo de U-SQL no Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Criar um TVF

No script de U-SQL anterior, repetido o uso de EXTRAÇÃO para ler a partir do mesmo ficheiro de origem. Com o U-SQL com valor de tabela função (TVF), pode encapsular os dados para futura reutilização.  

O seguinte script cria um TVF chamado `Searchlog()` a base de dados predefinida e o esquema:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

O script seguinte mostra como utilizar a TVF que foi definida no script anterior:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Criar vistas

Se tiver uma expressão de consulta única, em vez de um TVF pode utilizar uma vista de U-SQL para encapsular essa expressão.

O seguinte script cria uma vista denominada `SearchlogView` a base de dados predefinida e o esquema:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

O script a seguir demonstra o uso da vista definida:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Criar tabelas
Como com as tabelas de base de dados relacional, com o U-SQL pode criar uma tabela com um esquema predefinido ou criar uma tabela que infere o esquema da consulta que preenche a tabela (também conhecido como CREATE TABLE AS SELECT ou CTAS).

Crie uma base de dados e duas tabelas usando o seguinte script:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Consulta tabelas
Pode consultar as tabelas, tais como aqueles criados no script anterior, da mesma forma que consulte os ficheiros de dados. Em vez de criar um conjunto de linhas utilizando a EXTRAÇÃO, agora pode ver o nome da tabela.

Para ler a partir de tabelas, modifique o script de transformação que utilizou anteriormente:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Atualmente, não é possível executar um, SELECIONE numa tabela no mesmo script como aquele onde criou a tabela.

## <a name="next-steps"></a>Próximos Passos
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
