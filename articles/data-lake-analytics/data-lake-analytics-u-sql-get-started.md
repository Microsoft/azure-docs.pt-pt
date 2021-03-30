---
title: Começar com a linguagem U-SQL em Azure Data Lake Analytics
description: Aprenda os fundamentos da língua U-SQL em Azure Data Lake Analytics. Escreva a sua primeira consulta usando variáveis para dados extra de ficheiros, transforme o conjunto de linhas e os dados agregados.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219926"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Começar com u-SQL em Azure Data Lake Analytics

U-SQL é um idioma que combina SQL declarativo com imperativo C# para permitir que você processe dados em qualquer escala. Através da capacidade de consulta distribuída e escalável do U-SQL, pode analisar eficientemente dados em lojas relacionais, como a Base de Dados Azure SQL. Com o U-SQL, pode processar dados não estruturados aplicando esquemas na leitura e inserindo lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que lhe dá um controlo fino sobre como executar em escala.

## <a name="learning-resources"></a>Recursos de aprendizagem

* O [U-SQL Tutorial](https://aka.ms/usqltutorial) proporciona uma caminhada guiada através da maior parte da língua U-SQL. Este documento é recomendado de leitura para todos os desenvolvedores que desejam aprender U-SQL.
* Para obter informações detalhadas sobre a **sintaxe linguística U-SQL,** consulte a [Referência linguística U-SQL](/u-sql/).
* Para compreender a **filosofia de design U-SQL,** consulte o post de blog do Estúdio Visual [introduzindo u-SQL – uma linguagem que torna o processamento de big data fácil.](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

## <a name="prerequisites"></a>Pré-requisitos

Antes de analisar as amostras U-SQL neste documento, leia e complete [Tutorial: Desenvolva scripts U-SQL usando Ferramentas do Lago de Dados para Estúdio Visual](data-lake-analytics-data-lake-tools-get-started.md). Este tutorial explica a mecânica da utilização do U-SQL com Azure Data Lake Tools para o Visual Studio.

## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O seguinte script U-SQL é simples e permite-nos explorar muitos aspetos da linguagem U-SQL.

```usql
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

Este guião não tem nenhum passo de transformação. Lê a partir do ficheiro de origem chamado `SearchLog.tsv` , schematiza-o e escreve o conjunto de linha de volta para um ficheiro chamado SearchLog-first-u-sql.csv.

Note o ponto de interrogação ao lado do tipo de dados no `Duration` campo. Significa que o `Duration` campo pode ser nulo.

### <a name="key-concepts"></a>Conceitos-chave

* **Variáveis de linha**: Cada expressão de consulta que produz um conjunto de linha pode ser atribuída a uma variável. U-SQL segue o padrão de nomeação variável T-SQL `@searchlog` (, por exemplo) no script.
* A palavra-chave **EXTRACT** lê dados de um ficheiro e define o esquema na leitura. `Extractors.Tsv` é um extrator U-SQL incorporado para ficheiros de valor separados por separados. Pode desenvolver extratores personalizados.
* O **OUTPUT** escreve dados de um conjunto de linhas para um ficheiro. `Outputters.Csv()` é um outputter U-SQL incorporado para criar um ficheiro de valor separado em vírgula. Pode desenvolver outputters personalizados.

### <a name="file-paths"></a>Caminhos de arquivo

As declarações EXTRACT e OUTPUT utilizam caminhos de ficheiro. Os caminhos dos ficheiros podem ser absolutos ou relativos:

Este caminho de ficheiro absoluto se refere a um ficheiro numa Data Lake Store denominado `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Este seguinte caminho de ficheiro começa com `"/"` . Refere-se a um ficheiro na conta padrão da Data Lake Store:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Use variáveis escalar

Pode utilizar variáveis escalar também para facilitar a manutenção do seu script. O script anterior do U-SQL também pode ser escrito como:

```usql
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
```

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas

Utilizar **SELECT** para transformar conjuntos de linhas:

```usql
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
```

A cláusula WHERE utiliza uma [expressão C# Boolean](/dotnet/csharp/language-reference/operators/index). Pode utilizar a linguagem de expressão C# para fazer as suas próprias expressões e funções. Pode ainda realizar filtragem mais complexa combinando-as com conjunções lógicas (ANDs) e disjunções (RAMs).

O seguinte script utiliza o método DateTime.Parse e uma conjunção.

```usql
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
```

 >[!NOTE]
 >A segunda consulta está a funcionar com base no resultado do primeiro conjunto de linha, o que cria um composto dos dois filtros. Também pode reutilizar um nome variável, e os nomes são traçados lexicamente.

## <a name="aggregate-rowsets"></a>Conjuntos de linha agregados

U-SQL dá-lhe a ordem familiar por, GRUPO BY, e agregações.

A seguinte consulta encontra a duração total por região e, em seguida, apresenta as cinco melhores durações em ordem.

Os conjuntos de linha U-SQL não preservam a sua encomenda para a próxima consulta. Assim, para encomendar uma saída, é necessário adicionar ORDER BY à declaração OUTPUT:

```usql
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
```

A cláusula U-SQL ORDER BY requer a utilização da cláusula FETCH numa expressão SELECT.

A cláusula U-SQL HAVING pode ser usada para restringir a saída a grupos que satisfaçam a condição DE TER:

```usql
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
```

Para cenários avançados de agregação, consulte a documentação de referência U-SQL para [funções agregadas, analíticas e de referência](/u-sql/built-in-functions)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)