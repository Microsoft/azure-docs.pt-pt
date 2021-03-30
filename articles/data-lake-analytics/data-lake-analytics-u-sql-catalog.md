---
title: Use o catálogo U-SQL em Azure Data Lake Analytics
description: Saiba como utilizar o catálogo U-SQL para partilhar códigos e dados. Criar funções de valor de mesa, criar vistas, criar tabelas e questioná-las.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220079"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Começa com o Catálogo U-SQL em Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Criar uma TVF

No script U-SQL anterior, repetiu a utilização do EXTRACT para ler a partir do mesmo ficheiro de origem. Com a função de valor da tabela U-SQL (TVF), pode encapsular os dados para futura reutilização.  

O seguinte script cria uma TVF chamada `Searchlog()` na base de dados padrão e esquema:

```usql
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

O seguinte guião mostra-lhe como usar o TVF que foi definido no script anterior:

```usql
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

Se tiver uma única expressão de consulta, em vez de uma TVF, pode utilizar uma vista U-SQL para encapsular essa expressão.

O seguinte script cria uma vista chamada `SearchlogView` na base de dados padrão e esquema:

```usql
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

O seguinte guião demonstra a utilização da visão definida:

```usql
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
Tal como acontece com as tabelas de bases de dados relacionais, com o U-SQL pode criar uma tabela com um esquema predefinido ou criar uma tabela que infera o esquema da consulta que povoa a tabela (também conhecida como TABELA CREATE AS SELECT ou CTAS).

Crie uma base de dados e duas tabelas utilizando o seguinte script:

```usql
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
Pode consultar tabelas, como as criadas no script anterior, da mesma forma que consulta os ficheiros de dados. Em vez de criar um conjunto de linhas utilizando EXTRACT, agora pode consultar o nome da tabela.

Para ler nas tabelas, modifique o script de transformação que usou anteriormente:

```usql
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
 >Atualmente, não é possível executar um SELECT numa tabela no mesmo script que aquele em que criou a tabela.

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio | Azure](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
