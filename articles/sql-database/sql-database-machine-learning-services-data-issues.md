---
title: Trabalhar com tipos de dados SQL e R e objetos
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como trabalhar com tipos de dados e objetos de dados em R com a base de dados do SQL Azure com o Machine Learning Services (pré-visualização), incluindo problemas comuns que poderá encontrar.
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
ms.openlocfilehash: 069a2a5b3b26bf517b57034f05ab7080ab392319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702525"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Trabalhar com dados de R e SQL no SQL da base de dados serviços Azure Machine Learning (pré-visualização)

Este artigo aborda alguns dos problemas comuns que poderá encontrar ao mover dados entre o R e a base de dados SQL no [Machine Learning Services (com R) na base de dados do Azure SQL](sql-database-machine-learning-services-overview.md). A experiência de que obter por meio deste exercício fornece em segundo plano essencial ao trabalhar com dados no seu próprio script.

Problemas comuns que poderá encontrar incluem:

- Tipos de dados, às vezes, não correspondem
- Que podem ocorrer conversões implícitas
- Por vezes, são necessárias operações de CAST e convert
- R e SQL usam objetos de dados diferentes

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo Nestes exercícios, primeiro tem de ter uma SQL database do Azure com o Machine Learning Services (com R) ativada. Durante a pré-visualização pública, Microsoft irá carregar e ativar o machine learning para a base de dados nova ou existente. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

- Certifique-se de que instalou a versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Pode executar scripts R com outro gestão de bases de dados ou ferramentas de consulta, mas este início rápido irá utilizar o SSMS.

## <a name="working-with-a-data-frame"></a>Trabalhar com um quadro de dados

Quando o script retorna os resultados de R para o SQL, tem de devolver os dados como um **data. frame**. Qualquer outro tipo de objeto que gerar no seu script - se de que ser uma lista, fator, vetor ou dados binários - deve ser convertido para um quadro de dados, se quiser saída-lo como parte dos resultados do procedimento armazenado. Felizmente, existem várias funções de R para suportar a alteração de outros objetos para um quadro de dados. Pode, inclusivamente, a serialização de um modelo de binário e retorná-lo num quadro de dados, terá de efetuar neste artigo.

Em primeiro lugar, vamos experimentar alguns objetos básicos de R - vetores, matrizes e listas - e ver como a conversão para um quadro de dados é alterada a saída transmitida para o SQL.

Comparar esses dois scripts de "Hello World" em R. Os scripts parecem quase idênticos, mas o primeiro método retorna uma única coluna de três valores, enquanto o segundo retorna três colunas com um único valor a cada um.

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

A resposta pode ser normalmente encontrada ao utilizar o R `str()` comando. Adicione a função `str(object_name)` em qualquer lugar no script R para que os dados de esquema do objeto de R especificado retornados como uma mensagem meramente informativa. Pode ver as mensagens na **mensagens** separador no SSMS.

Para descobrir por que o exemplo 1 e 2 de exemplo tem esses resultados diferentes, insira a linha `str(OutputDataSet)` no final o `@script` definição de variável em cada instrução, como esta:

**Exemplo 1 com a função de str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exemplo 2 com a função de str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Agora, reveja o texto na **mensagens** para ver por que a saída é diferente.

**Resultados - exemplo 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultados - exemplo 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Como pode ver, uma pequena alteração na sintaxe de R tinha um grande impacto sobre o esquema dos resultados. Para todos os detalhes, as diferenças nos tipos de dados de R são explicadas em detalhe na *estruturas de dados* secção ["Advanced R" por Hadley Wickham](http://adv-r.had.co.nz).

Por enquanto, apenas tenha em atenção que tem de verificar os resultados esperados, quando impor objetos de R em quadros de dados.

> [!TIP]
> Também pode utilizar as funções de identidade do R, como `is.matrix`, `is.vector`, para devolver informações sobre a estrutura de dados internos.

## <a name="implicit-conversion-of-data-objects"></a>Conversão implícita de objetos de dados

Cada objeto de dados do R tem suas próprias regras para a forma como os valores são processados quando combinado com outros objetos de dados se os objetos de dados de dois têm o mesmo número de dimensões, ou se qualquer objeto de dados contém tipos de dados heterogéneos.

Por exemplo, suponha que pretende executar uma multiplicação de matriz com o R. Pretende multiplicar uma matrix de coluna única com os três valores de uma matriz com quatro valores e, como resultado a esperar uma matriz de 4 x 3.

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

Agora, execute o seguinte script.

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

Nos bastidores, a coluna de três valores é convertida numa matriz de coluna única. Uma vez que uma matriz é apenas um caso especial de uma matriz em R, a matriz `y` implicitamente é forçada a uma matriz de coluna única para que os dois argumentos estão em conformidade com.

**Resultados**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

No entanto, tenha em atenção o que acontece quando altera o tamanho da matriz `y`.

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

Agora o R devolve um valor único como o resultado.

**Resultados**
    
|Col1|
|---|
|1542|

Porquê? Neste caso, uma vez que os dois argumentos podem ser tratados como vetores mesma duração, o R devolve o produto de interna como uma matriz.  Este é o comportamento esperado de acordo com as regras de álgebra linear. No entanto, isso poderia causar problemas se a sua aplicação a jusante espera que o esquema de saída para alterar nunca!

## <a name="merge-or-multiply-columns-of-different-length"></a>Intercalar ou multiplique as colunas de comprimento diferente

R fornece grande flexibilidade para trabalhar com vetores de tamanhos diferentes e para combinar essas estruturas de tipo de coluna em quadros de dados. Listas de vetores podem ser semelhante uma tabela, mas eles não seguem a todas as regras que regem a tabelas de base de dados.

Por exemplo, o script a seguir define uma matriz numérico de comprimento 6 e armazena-os na variável R `df1`. A matriz numérica, em seguida, é combinada com inteiros da tabela RTestData (criado acima) que contém três (3) valores, para fazer um novo quadro de dados, `df2`.

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

Para preencher o quadro de dados, R Repete os elementos obtidos a partir do RTestData quantas vezes conforme necessário para corresponder ao número de elementos na matriz `df1`.

**Resultados**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Lembre-se de que um quadro de dados apenas é semelhante a uma tabela, mas é, na verdade, uma lista de vetores.

## <a name="cast-or-convert-sql-data"></a>CAST ou convert dados do SQL

R e SQL, não utilizam os mesmos tipos de dados, para que quando executa uma consulta em SQL para obter dados e, em seguida, passá-lo para o runtime do R, algum tipo de conversão implícita normalmente decorre. Outro conjunto de conversões ocorre quando devolve dados de R para o SQL.

- SQL envia os dados da consulta para o processo de R e converte-o para uma representação interna para uma maior eficiência.
- O tempo de execução do R carrega os dados para uma variável de data. frame e executa as suas próprias operações nos dados.
- O motor de base de dados devolve os dados para o SQL com uma conexão interna segura e apresenta os dados em termos de tipos de dados SQL.
- Obter os dados ao ligar ao SQL através de uma biblioteca de cliente ou de rede que pode emitir consultas SQL e lidar com conjuntos de dados em tabela. Esta aplicação de cliente pode potencialmente afetar os dados de outras formas.

Para ver como isso funciona, execute uma consulta como no [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) armazém de dados. Esta vista devolve dados de vendas utilizados na criação de previsões.

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
> Pode utilizar qualquer versão do AdventureWorks, ou criar uma consulta diferente com uma base de dados do seu próprio. O ponto é tentar lidar com alguns dados que contém texto, datetime e valores numéricos.

Agora, tente usando esta consulta como entrada para o procedimento armazenado.

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

Se obtiver um erro, provavelmente terá de efetuar algumas edições para o texto da consulta. Por exemplo, o predicado de cadeia de caracteres na cláusula WHERE têm de estar entre por dois conjuntos de plicas.

Depois de obter a consulta a trabalhar, reveja os resultados da `str` função para ver como o R processa os dados de entrada.

**Resultados**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A coluna de datetime foi processada com o tipo de dados do R **POSIXct**.
- A coluna de texto "ProductSeries" foi identificado como um **fator**, que significa que uma variável categórica. Valores de cadeia de caracteres são processadas como fatores, por predefinição. Se passar uma cadeia de caracteres para o R, é convertido num número inteiro para utilização interna e, em seguida, mapeado para a cadeia de saída.

## <a name="summary"></a>Resumo

Até mesmo para essas breves exemplos, pode ver a necessidade de verificar os efeitos da conversão de dados quando passar SQL consulta como entrada. Uma vez que alguns tipos de dados SQL não são suportados pelo R, considere estas formas de evitar erros:

- Testar os seus dados com antecedência e certifique-se de colunas ou valores no seu esquema que poderia ser um problema ao passado para código R.
- Especificar as colunas na sua origem de dados de entrada individualmente, em vez de usar `SELECT *`e saber como cada coluna será processada.
- Efetue conversões conforme necessário quando a preparação dos dados de entrada, para evitar surpresas.
- Evite colunas de passagem de dados (como GUIDS ou rowguids) que causam erros e não são úteis para modelagem.

Para obter mais informações sobre tipos de dados de R suportados e não suportados, consulte [tipos de dados e de bibliotecas de R](/sql/advanced-analytics/r/r-libraries-and-data-types.md).
