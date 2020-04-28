---
title: Começar com a linguagem U-SQL no Azure Data Lake Analytics
description: Aprenda o básico da língua U-SQL no Azure Data Lake Analytics. Escreva a sua primeira consulta usando variáveis para extra dados de ficheiros, transforme o conjunto de linhas e agregando dados.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71672827"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Começar com u-SQL em Azure Data Lake Analytics
U-SQL é uma linguagem que combina SQL declarativo com C# imperativo para permitir processar dados em qualquer escala. Através da capacidade de consulta estampada e distribuída do U-SQL, pode analisar eficientemente dados em lojas relacionais, como a Base de Dados Azure SQL. Com o U-SQL, pode processar dados não estruturados aplicando esquemas na leitura e inserindo lógica personalizada e UDFs. Além disso, a U-SQL inclui a extensibility que lhe dá um controlo fino sobre como executar em escala. 

## <a name="learning-resources"></a>Recursos de aprendizagem

* O [U-SQL Tutorial](https://aka.ms/usqltutorial) proporciona uma passagem guiada pela maior parte da língua U-SQL. Este documento é uma leitura recomendada para todos os desenvolvedores que pretendam aprender U-SQL.
* Para obter informações detalhadas sobre a **sintaxe linguística U-SQL,** consulte a [referência linguística U-SQL](https://docs.microsoft.com/u-sql/).
* Para compreender a filosofia de **design U-SQL,** consulte o blog do Estúdio Visual [introduzindo U-SQL – Uma linguagem que torna](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)o processamento de big data fácil .

## <a name="prerequisites"></a>Pré-requisitos

Antes de analisar as amostras u-SQL neste documento, leia e complete [o Tutorial: Desenvolva scripts U-SQL utilizando ferramentas](data-lake-analytics-data-lake-tools-get-started.md)de data lake para estúdio visual . Este tutorial explica a mecânica de usar u-SQL com ferramentas de lago de dados Azure para estúdio visual.

## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O seguinte script U-SQL é simples e permite-nos explorar muitos aspetos da língua U-SQL.

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

Este guião não tem passos de transformação. Lê-se no ficheiro `SearchLog.tsv`fonte chamado , schematize-o, e escreve o rowset de volta num ficheiro chamado SearchLog-first-u-sql.csv.

Note o ponto de interrogação `Duration` ao lado do tipo de dados no campo. Significa que `Duration` o campo pode ser nulo.

### <a name="key-concepts"></a>Conceitos-chave
* **Variáveis rowset**: Cada expressão de consulta que produz um conjunto de linhas pode ser atribuída a uma variável. U-SQL segue o padrão de nomeação`@searchlog`variável T-SQL (, por exemplo) no script.
* A palavra-chave **EXTRACT** lê os dados de um ficheiro e define o esquema na leitura. `Extractors.Tsv`é um extrator U-SQL incorporado para ficheiros separados por separados por separados. Pode desenvolver extratores personalizados.
* A **OUTPUT** escreve dados de um conjunto de linhas para um ficheiro. `Outputters.Csv()`é um outputter U-SQL incorporado para criar um ficheiro de valor separado de víra. Pode desenvolver saídas personalizadas.

### <a name="file-paths"></a>Caminhos de arquivo

As declarações EXTRACT e OUTPUT utilizam caminhos de ficheiros. Os caminhos de arquivo podem ser absolutos ou relativos:

Este caminho de ficheiro absoluto seguinte refere-se `mystore`a um ficheiro numa Data Lake Store com o nome:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Este caminho de `"/"`ficheiro que se segue começa com . Refere-se a um ficheiro na conta padrão data lake store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Use variáveis escalar

Também pode utilizar variáveis escalar para facilitar a manutenção do seu script. O script u-SQL anterior também pode ser escrito como:

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

Utilize o **SELECT** para transformar conjuntos de linhas:

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

A cláusula WHERE usa uma [expressão C# Boolean.](/dotnet/csharp/language-reference/operators/index) Pode usar a linguagem de expressão C# para fazer as suas próprias expressões e funções. Pode até realizar uma filtragem mais complexa combinando-as com conjunções lógicas (ANDs) e disjunções (ORs).

O seguinte script utiliza o método DateTime.Parse() e uma conjunção.

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
 >A segunda consulta está a funcionar no resultado do primeiro conjunto de linhas, o que cria um composto dos dois filtros. Também pode reutilizar um nome variável, e os nomes são lexicamente.

## <a name="aggregate-rowsets"></a>Conjuntos de linhas agregados
A U-SQL dá-lhe a ordem familiar BY, GROUP BY e agregações.

A seguinte consulta encontra a duração total por região, e depois exibe as cinco principais durações por ordem.

Os rowsets U-SQL não preservam a sua ordem para a próxima consulta. Assim, para encomendar uma saída, é necessário adicionar ORDER BY à declaração OUTPUT:

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

A cláusula U-SQL ORDER BY requer a utilização da cláusula FETCH numa expressão SELECT.

A cláusula U-SQL HAVING pode ser usada para restringir a saída a grupos que satisfaçam a condição DE TER:

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

Para cenários avançados de agregação, consulte a documentação de referência U-SQL para [funções agregadas, analíticas e de referência](/u-sql/built-in-functions)

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
