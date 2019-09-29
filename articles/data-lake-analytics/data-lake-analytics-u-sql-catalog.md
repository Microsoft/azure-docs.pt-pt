---
title: Usar o catálogo do U-SQL no Azure Data Lake Analytics
description: Saiba como usar o catálogo do U-SQL para compartilhar código e dados. Crie funções com valor de tabela, crie exibições, crie tabelas e consulte-as.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672853"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Introdução ao catálogo do U-SQL no Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Criar um TVF

No script U-SQL anterior, você repetiu o uso de EXTRACT para ler do mesmo arquivo de origem. Com a função com valor de tabela do U-SQL (TVF), você pode encapsular os dados para reutilização futura.  

O script a seguir cria um TVF `Searchlog()` chamado no banco de dados e esquema padrão:

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

O script a seguir mostra como usar o TVF que foi definido no script anterior:

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

## <a name="create-views"></a>Criar exibições

Se você tiver uma única expressão de consulta, em vez de uma TVF, poderá usar uma exibição U-SQL para encapsular essa expressão.

O script a seguir cria uma exibição `SearchlogView` chamada no banco de dados e esquema padrão:

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

O script a seguir demonstra o uso da exibição definida:

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
Assim como nas tabelas de banco de dados relacional, com o U-SQL, você pode criar uma tabela com um esquema predefinido ou criar uma tabela que infere o esquema da consulta que popula a tabela (também conhecida como CREATE TABLE como SELECT ou CTAS).

Crie um banco de dados e duas tabelas usando o seguinte script:

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
Você pode consultar tabelas, como aquelas criadas no script anterior, da mesma maneira que consulta os arquivos de dados. Em vez de criar um conjunto de linhas usando EXTRACT, agora você pode consultar o nome da tabela.

Para ler as tabelas, modifique o script de transformação que você usou anteriormente:

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
 >No momento, não é possível executar uma seleção em uma tabela no mesmo script que a tabela em que você criou.

## <a name="next-steps"></a>Próximos Passos
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
