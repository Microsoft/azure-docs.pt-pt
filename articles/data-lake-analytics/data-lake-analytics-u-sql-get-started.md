---
title: Introdução à linguagem U-SQL no Azure Data Lake Analytics
description: Aprenda os conceitos básicos da linguagem U-SQL no Azure Data Lake Analytics. Escreva sua primeira consulta usando variáveis para dados adicionais de arquivos, transforme o conjunto de linhas e agregue dados.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672827"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Introdução ao U-SQL no Azure Data Lake Analytics
O U-SQL é uma linguagem que combina SQL declarativo com imperativa C# para permitir que você processe dados em qualquer escala. Por meio do recurso escalonável e de consulta distribuída do U-SQL, você pode analisar com eficiência os dados em repositórios relacionais, como o banco de dados SQL do Azure. Com o U-SQL, você pode processar dados não estruturados aplicando o esquema na leitura e inserção de lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que oferece um controle refinado sobre como executar em escala. 

## <a name="learning-resources"></a>Recursos de aprendizagem

* O [tutorial do u-SQL](https://aka.ms/usqltutorial) fornece um passo a passos guiado da maior parte da linguagem u-SQL. Este documento é uma leitura recomendada para todos os desenvolvedores que desejam aprender sobre o U-SQL.
* Para obter informações detalhadas sobre a **sintaxe da linguagem u-SQL**, consulte a [referência da linguagem u-SQL](https://docs.microsoft.com/u-sql/).
* Para entender a **filosofia de design do U-SQL**, consulte a postagem do blog do Visual Studio [apresentando o U-SQL – uma linguagem que facilita o processamento de Big data](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de passar pelos exemplos de U-SQL neste documento, leia e conclua o [tutorial: desenvolver scripts U-SQL usando as ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Esse tutorial explica a mecânica do uso do U-SQL com o Ferramentas do Azure Data Lake para Visual Studio.

## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O script U-SQL a seguir é simples e nos permite explorar muitos aspectos da linguagem U-SQL.

```
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

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Esse script não tem nenhuma etapa de transformação. Ele lê a partir do arquivo de origem chamado `SearchLog.tsv`, esquematiza-o e grava o conjunto de linhas de volta em um arquivo chamado Searchlog-. csv.

Observe o ponto de interrogação ao lado do tipo de dados no campo `Duration`. Isso significa que o campo `Duration` poderia ser nulo.

### <a name="key-concepts"></a>Conceitos-chave
* **Variáveis de conjunto de linhas**: cada expressão de consulta que produz um conjunto de linhas pode ser atribuída a uma variável. O U-SQL segue o padrão de nomenclatura da variável T-SQL (`@searchlog`, por exemplo) no script.
* A palavra-chave **Extract** lê dados de um arquivo e define o esquema na leitura. `Extractors.Tsv` é um extrator U-SQL interno para arquivos de valor separado por tabulação. Você pode desenvolver extratores personalizados.
* A **saída** grava dados de um conjunto de linhas em um arquivo. `Outputters.Csv()` é um outemitinte U-SQL interno para criar um arquivo de valor separado por vírgulas. Você pode desenvolver outistores personalizados.

### <a name="file-paths"></a>Caminhos de arquivo

As instruções EXTRACT e OUTPUT usam caminhos de arquivo. Os caminhos de arquivo podem ser absolutos ou relativos:

Este caminho de arquivo absoluto a seguir refere-se a um arquivo em um Data Lake Store nomeado `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

O caminho de arquivo a seguir começa com `"/"`. Ele se refere a um arquivo na conta de Data Lake Store padrão:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Usar variáveis escalares

Você também pode usar variáveis escalares para facilitar a manutenção do script. O script U-SQL anterior também pode ser escrito como:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas

Use **Select** para transformar conjuntos de linhas:

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE usa uma [ C# expressão booleana](/dotnet/csharp/language-reference/operators/index). Você pode usar a C# linguagem de expressão para fazer suas próprias expressões e funções. Você pode até mesmo executar filtragem mais complexa combinando-os com conconjuntos lógicos (ANDs) e disjunçãos (ORs).

O script a seguir usa o método DateTime. Parse () e uma conjunção.

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

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >A segunda consulta está operando no resultado do primeiro conjunto de linhas, que cria uma composição dos dois filtros. Você também pode reutilizar um nome de variável e os nomes têm o escopo definido lexicalmente.

## <a name="aggregate-rowsets"></a>Agregar conjuntos de linhas
O U-SQL oferece a você as conhecidas ORDENAr, agrupar por e agregações.

A consulta a seguir localiza a duração total por região e, em seguida, exibe as cinco principais durações na ordem.

Os conjuntos de linhas U-SQL não preservam sua ordem para a próxima consulta. Portanto, para ordenar uma saída, você precisa adicionar ORDER BY à instrução OUTPUT:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

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

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

A cláusula ORDER BY do U-SQL requer o uso da cláusula FETCH em uma expressão SELECT.

A cláusula U-SQL HAVING pode ser usada para restringir a saída a grupos que atendem à condição HAVING:

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

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Para cenários de agregação avançada, consulte a documentação de referência do U-SQL para [funções de agregação, analíticas e de referência](/u-sql/built-in-functions)

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
