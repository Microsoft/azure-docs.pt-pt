---
title: Trabalhar com tipos e objetos de dados R e SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como trabalhar com tipos de dados e objetos de dados em R com Azure SQL Database usando Serviços de Aprendizagem automática (pré-visualização), incluindo questões comuns que poderá encontrar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d7f33dd782b46afa1aa3845d2acb5712a76ea420
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324203"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Trabalhar com dados R e SQL em Azure SQL Database Machine Learning Services (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo discute algumas das questões comuns que pode encontrar ao mover dados entre R e Azure SQL Database in [Machine Learning Services (com R) na Base de Dados Azure SQL](machine-learning-services-overview.md). A experiência que você ganha através deste exercício fornece fundo essencial ao trabalhar com dados no seu próprio script.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Questões comuns que pode encontrar incluem:

- Os tipos de dados às vezes não combinam
- Conversões implícitas podem ocorrer
- As operações de elenco e conversão são, por vezes, necessárias
- R e SQL usam diferentes objetos de dados

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, você deve primeiro ter [Azure SQL Database com Serviços de Aprendizagem automática (com R)](machine-learning-services-overview.md) ativado.

- Certifique-se de que instalou o mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R utilizando outras ferramentas de gestão de bases de dados ou de consulta, mas neste arranque rápido utilizará SSMS.

## <a name="working-with-a-data-frame"></a>Trabalhar com um quadro de dados

Quando o seu script retorna os resultados de R para SQL, deve devolver os dados como um **data.frame**. Qualquer outro tipo de objeto que gere no seu script - seja uma lista, fator, vetor ou dados binários - deve ser convertido para um quadro de dados se quiser desaudá-lo como parte dos resultados do procedimento armazenado. Felizmente, existem múltiplas funções R para suportar a mudança de outros objetos para um quadro de dados. Pode até serializar um modelo binário e devolvê-lo num quadro de dados, o que fará mais tarde neste artigo.

Primeiro, vamos experimentar alguns objetos R básicos - vetores, matrizes e listas - e ver como a conversão para um quadro de dados altera a saída passada para SQL.

Compare estes dois scripts "Hello World" em R. Os scripts parecem quase idênticos, mas o primeiro devolve uma única coluna de três valores, enquanto a segunda devolve três colunas com um único valor cada.

**Exemplo 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Exemplo 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Por que os resultados são tão diferentes?

A resposta pode geralmente ser encontrada usando o `str()` comando R. Adicione a função `str(object_name)` em qualquer lugar do seu script R para que o esquema de dados do objeto R especificado seja devolvido como uma mensagem informativa. Pode ver as mensagens no separador **Mensagens** em SSMS.

Para descobrir por que razão o Exemplo 1 e o Exemplo 2 têm resultados tão diferentes, insira a linha `str(OutputDataSet)` no final da `@script` definição variável em cada declaração, como esta:

**Exemplo 1 com função de str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exemplo 2 com função de str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Agora, reveja o texto em **Mensagens** para ver por que a saída é diferente.

**Resultados - Exemplo 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultados - Exemplo 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Como pode ver, uma ligeira alteração na sintaxe R teve um grande efeito no esquema dos resultados. Para todos os detalhes, as diferenças nos tipos de dados R são explicadas em detalhes na secção *Estruturas de Dados* em ["Advanced R" de Hadley Wickham](http://adv-r.had.co.nz).

Por enquanto, esteja ciente de que precisa de verificar os resultados esperados ao coagir os objetos R em quadros de dados.

> [!TIP]
> Também pode utilizar funções de identidade R, tais `is.matrix` como, `is.vector` para devolver informações sobre a estrutura interna de dados.

## <a name="implicit-conversion-of-data-objects"></a>Conversão implícita de objetos de dados

Cada objeto de dados R tem as suas próprias regras para a forma como os valores são tratados quando combinados com outros objetos de dados se os dois objetos de dados tiverem o mesmo número de dimensões, ou se algum objeto de dados contiver tipos de dados heterogéneos.

Por exemplo, assuma que pretende realizar multiplicação de matriz usando R. Pretende-se multiplicar uma matriz de uma coluna com os três valores por uma matriz com quatro valores, e esperar uma matriz 4x3 como resultado.

Primeiro, crie uma pequena tabela de dados de teste.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Agora executar o seguinte roteiro.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Sob as capas, a coluna de três valores é convertida numa matriz de coluna única. Como uma matriz é apenas um caso especial de uma matriz em R, a matriz `y` é implicitamente coagida a uma matriz de uma coluna para fazer os dois argumentos se conformarem.

**Resultados**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

No entanto, note o que acontece quando se muda o tamanho da matriz `y` .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Agora R devolve um único valor como resultado.

**Resultados**
    
|Col1|
|---|
|1542|

Porquê? Neste caso, como os dois argumentos podem ser manuseados como vetores do mesmo comprimento, R devolve o produto interno como uma matriz.  Este é o comportamento esperado de acordo com as regras da álgebra linear. No entanto, pode causar problemas se a sua aplicação a jusante espera que o esquema de saída nunca mude!

## <a name="merge-or-multiply-columns-of-different-length"></a>Fundir ou multiplicar colunas de comprimento diferente

R proporciona uma grande flexibilidade para trabalhar com vetores de diferentes tamanhos, e para combinar estas estruturas semelhantes a colunas em quadros de dados. Listas de vetores podem parecer uma mesa, mas não seguem todas as regras que regem as tabelas de bases de dados.

Por exemplo, o seguinte script define uma matriz numérica de comprimento 6 e armazena-o na variável R `df1` . A matriz numérica é então combinada com os inteiros da tabela RTestData (criada acima) que contém três (3) valores, para fazer um novo quadro de dados, `df2` .

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Para preencher o quadro de dados, R repete os elementos recuperados do RTestData quantas vezes forem necessários para corresponder ao número de elementos na matriz `df1` .

**Resultados**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Lembre-se que um quadro de dados só parece uma mesa, mas na verdade é uma lista de vetores.

## <a name="cast-or-convert-sql-data"></a>Elenco ou conversão de dados SQL

R e SQL não usam os mesmos tipos de dados, por isso, quando você executou uma consulta em SQL para obter dados e, em seguida, passar isso para o tempo de execução R, algum tipo de conversão implícita geralmente ocorre. Outro conjunto de conversões ocorre quando devolve dados de R a SQL.

- O SQL empurra os dados da consulta para o processo R e converte-os numa representação interna para uma maior eficiência.
- O tempo de execução R carrega os dados numa variável data.frame e executa as suas próprias operações nos dados.
- O motor de base de dados devolve os dados ao SQL utilizando uma ligação interna segura e apresenta os dados em termos de tipos de dados SQL.
- Obtém os dados ligando-se ao SQL utilizando um cliente ou biblioteca de rede que pode emitir consultas SQL e lidar com conjuntos de dados tabulares. Esta aplicação do cliente pode potencialmente afetar os dados de outras formas.

Para ver como isto funciona, executar uma consulta como esta no armazém de dados [AdventureWorksDW.](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) Esta vista devolve os dados de vendas utilizados na criação de previsões.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Pode utilizar qualquer versão do AdventureWorks ou criar uma consulta diferente utilizando uma base de dados própria. O objetivo é tentar lidar com alguns dados que contenham texto, data e valores numéricos.

Agora, tente usar esta consulta como entrada para o procedimento armazenado.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Se tiver um erro, provavelmente terá de fazer algumas edições para o texto da consulta. Por exemplo, o predicado de cadeia na cláusula WHERE deve ser incluído por dois conjuntos de aspas únicas.

Depois de obter a consulta funcionando, reveja os resultados da `str` função para ver como R trata os dados de entrada.

**Resultados**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A coluna data foi processada utilizando o tipo de dados R, **POSIXct**.
- A coluna de texto "ProductSeries" foi identificada como um **fator,** o que significa uma variável categórica. Os valores de cadeia são tratados como fatores por defeito. Se passar uma corda para R, é convertido para um inteiro para uso interno e, em seguida, mapeado de volta para a cadeia na saída.

## <a name="summary"></a>Resumo

Mesmo a partir destes exemplos curtos, você pode ver a necessidade de verificar os efeitos da conversão de dados ao passar as consultas SQL como entrada. Como alguns tipos de dados SQL não são suportados por R, considere estas formas de evitar erros:

- Teste os seus dados com antecedência e verifique as colunas ou valores no seu esquema que podem ser um problema quando passado para código R.
- Especifique as colunas na sua fonte de dados de entrada individualmente, em vez de usar `SELECT *` , e saiba como cada coluna será manuseada.
- Execute moldes explícitos conforme necessário ao preparar os seus dados de entrada, para evitar surpresas.
- Evite passar colunas de dados (como GUIDS ou guias de linha) que causam erros e não são úteis para modelar.

Para obter mais informações sobre tipos de dados R suportados e não suportados, consulte [bibliotecas R e tipos de dados](/sql/advanced-analytics/r/r-libraries-and-data-types).
