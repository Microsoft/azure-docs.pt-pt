---
title: Introdução à linguagem U-SQL no Azure Data Lake Analytics
description: Aprenda os fundamentos básicos da linguagem U-SQL no Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 9de5c7228944bd0448d9dfa833ef223140ccf0e8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488716"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Introdução ao U-SQL no Azure Data Lake Analytics
U-SQL é uma linguagem que combina SQL declarativa com imperativo c# para permitem-lhe processar dados em qualquer escala. Por meio da capacidade de dimensionável e de consulta distribuído de U-SQL, pode analisar eficazmente os dados em repositórios relacionais, como a base de dados do Azure SQL. U-SQL, pode processar os dados não estruturados, ao aplicar o esquema na leitura e inserção de lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que lhe dá controle refinado sobre como executar à escala. 

## <a name="learning-resources"></a>Recursos de aprendizado

* O [U-SQL Tutorial](https://aka.ms/usqltutorial) fornece instruções orientadas da maior parte da linguagem U-SQL. Este documento é leitura recomendado para todos os desenvolvedores que desejam saber mais sobre U-SQL.
* Para obter informações detalhadas sobre o **sintaxe de linguagem U-SQL**, consulte a [referência de linguagem U-SQL](https://go.microsoft.com/fwlink/p/?LinkId=691348).
* Para compreender os **filosofia de design de U-SQL**, consulte a mensagem de blogue do Visual Studio [introdução de U-SQL – uma linguagem que torna fácil de processamento de dados grande](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir os exemplos de U-SQL neste documento, ler e concluir [Tutorial: Desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Esse tutorial explica a mecânica do uso do U-SQL com ferramentas do Azure Data Lake para Visual Studio.

## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O seguinte script U-SQL é simples e permite-nos que exploram muitos aspetos a linguagem U-SQL.

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

Este script não tem quaisquer passos de transformação. Lê a partir do arquivo de origem chamado `SearchLog.tsv`schematizes-lo e grava o conjunto de linhas de volta para um arquivo chamado SearchLog-first-u-sql.csv.

Tenha em atenção o ponto de interrogação junto os dados de digitar o `Duration` campo. Isso significa que o `Duration` campo pode ser nulo.

### <a name="key-concepts"></a>Conceitos-chave
* **Variáveis de conjunto de linhas**: Cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. U-SQL segue o padrão de nomenclatura de variável T-SQL (`@searchlog`, por exemplo) no script.
* O **EXTRAIR** lê dados a partir de um ficheiro de palavra-chave e define o esquema na leitura. `Extractors.Tsv` é um extrator de U-SQL incorporada para ficheiros de valores separador separados. Pode desenvolver extratores personalizados.
* O **saída** escreve dados de um conjunto de linhas para um ficheiro. `Outputters.Csv()` é um outputter de U-SQL incorporada para criar um ficheiro de separados por vírgulas. Pode desenvolver operadores personalizados.

### <a name="file-paths"></a>Caminhos de ficheiro

As instruções de EXTRAÇÃO e saída de utilizar caminhos de ficheiro. Caminhos de ficheiro podem ser absoluto ou relativo:

Este caminho absoluto do ficheiro seguinte refere-se num arquivo num Store de Lake de dados com o nome `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Este caminho de ficheiro seguinte começa com `"/"`. Ele se refere a um ficheiro na conta de Data Lake Store predefinida:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Utilizar variáveis escalares

Também pode utilizar variáveis de escalares para facilitar a manutenção de script. O script de U-SQL anterior também pode ser escrito como:

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

## <a name="transform-rowsets"></a>Transformar os conjuntos de linhas

Uso **SELECIONE** para transformar os conjuntos de linhas:

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

A cláusula WHERE usa um [expressão c# booleana](/dotnet/csharp/language-reference/operators/index). Pode utilizar a linguagem de expressão do c# para fazer as suas próprias expressões e funções. Pode até mesmo executar filtragem mais complexas ao combiná-los com lógicas conjunções (ANDs) e disjunctions (ORs).

O script seguinte utiliza o método DateTime.Parse() e um conjunto.

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
 >A segunda consulta está a funcionar no resultado do primeiro conjunto de linhas, o que cria um composto de dois filtros. Também pode reutilizar um nome de variável e os nomes estão confinados léxico.

## <a name="aggregate-rowsets"></a>Conjuntos de linhas agregados
U-SQL dá-lhe o familiar ORDER BY, GROUP BY e agregações.

A consulta seguinte localiza a duração total por região e, em seguida, mostra as principais durações de cinco por ordem.

Conjuntos de linhas de U-SQL não preservem a ordem para a consulta seguinte. Portanto, para solicitar uma saída, precisa adicionar ORDER BY à declaração de saída:

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

A cláusula ORDER BY U-SQL, é necessário usar a cláusula FETCH numa expressão SELECT.

A cláusula de U-SQL que pode ser utilizada para restringir a saída aos grupos que satisfaçam a condição HAVING:

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

Para cenários avançados de agregação, consulte a documentação de referência de U-SQL para [agregar, análise e as funções de referência](/u-sql/built-in-functions)

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
