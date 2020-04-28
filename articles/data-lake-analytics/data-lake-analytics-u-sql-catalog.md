---
title: Use o catálogo U-SQL em Azure Data Lake Analytics
description: Aprenda a usar o catálogo U-SQL para partilhar códigos e dados. Criar funções de valor de tabela, criar vistas, criar tabelas e questioná-las.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71672853"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Começar com o Catálogo U-SQL em Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Criar uma TVF

No script U-SQL anterior, repetiu o uso de EXTRACT para ler a partir do mesmo ficheiro fonte. Com a função u-SQL de valor de tabela (TVF), pode encapsular os dados para futura reutilização.  

O seguinte script cria `Searchlog()` uma TVF chamada na base de dados padrão e esquema:

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

O seguinte guião mostra-lhe como usar a TVF que foi definida no script anterior:

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

Se tiver uma única expressão de consulta, em vez de uma TVF, pode utilizar uma VISÃO U-SQL para encapsular essa expressão.

O seguinte script cria `SearchlogView` uma visão chamada na base de dados padrão e esquema:

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

O seguinte guião demonstra o uso da visão definida:

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
Tal como acontece com as tabelas de bases de dados relacionais, com o U-SQL pode criar uma tabela com um esquema pré-definido ou criar uma tabela que infere o esquema a partir da consulta que povoa a tabela (também conhecida como CREATE TABLE AS SELECT ou CTAS).

Criar uma base de dados e duas tabelas utilizando o seguinte script:

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
Pode consultar tabelas, como as criadas no script anterior, da mesma forma que consulta os ficheiros de dados. Em vez de criar um conjunto de linhas utilizando EXTRACT, agora pode consultar o nome da mesa.

Para ler a partir das tabelas, modifique o script de transformação que utilizou anteriormente:

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
 >Atualmente, não é possível executar um SELECT numa mesa no mesmo guião que aquele em que criou a tabela.

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
