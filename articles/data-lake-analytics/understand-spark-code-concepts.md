---
title: Compreenda os conceitos de código Apache Spark para desenvolvedores U-SQL do Lago de Dados Azure.
description: Este artigo descreve conceitos Apache Spark para ajudar os desenvolvedores u-SQL a entender conceitos de código Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: a384db9c3c0b4beee6063fd503abadcb4c6b5158
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016955"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Compreender o código Apache Spark para desenvolvedores U-SQL

Esta secção fornece orientações de alto nível sobre a transformação de scripts U-SQL para Apache Spark.

- Começa com uma [comparação dos paradigmas de processamento das duas línguas](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Fornece dicas sobre como:
   - [Transforme scripts](#transform-u-sql-scripts) incluindo expressões de [rowset](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) da U-SQL
   - [Código .NET](#transform-net-code)
   - [Tipos de dados](#transform-typed-values)
   - [Objetos de catálogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Compreender a linguagem U-SQL e a Spark e os paradigmas de processamento

Antes de começar a migrar os scripts U-SQL do Azure Data Lake Analytics para a Spark, é útil compreender as filosofias gerais de linguagem e processamento dos dois sistemas.

U-SQL é uma linguagem de consulta declarativa semelhante a SQL que usa um paradigma de fluxo de dados e permite incorporar e escalar facilmente o código de utilizador escrito em .NET (por exemplo C#), Python e R. As extensões de utilizador podem implementar expressões simples ou funções definidas pelo utilizador, mas também podem fornecer ao utilizador a capacidade de implementar os chamados operadores definidos pelo utilizador que implementam operadores personalizados para realizar transformações de nível de linha, extrações e saída de escrita.

Spark é um quadro de escala que oferece várias ligações linguísticas em Scala, Java, Python, .NET etc. onde escreve principalmente o seu código numa destas línguas, cria abstrações de dados chamadas conjuntos de dados distribuídos resilientes (RDD), dataframes e conjuntos de dados e, em seguida, usa uma linguagem específica de domínio (DSL) semelhante a LINQ para transformá-los. Também fornece o SparkSQL como uma sublinguagem declarativa sobre o dataframe e abstrações de conjunto de dados. A DSL disponibiliza duas categorias de operações, transformações e ações. Aplicar transformações nas abstrações de dados não executará a transformação, mas sim construirá o plano de execução que será submetido para avaliação com uma ação (por exemplo, escrever o resultado numa tabela ou arquivo temporário, ou imprimir o resultado).

Assim, ao traduzir um script U-SQL para um programa Spark, terá de decidir qual a linguagem que pretende utilizar para pelo menos gerar a abstração do quadro de dados (que é atualmente a abstração de dados mais utilizada) e se pretende escrever as transformações declarativas de fluxo de dados utilizando o DSL ou o SparkSQL. Em alguns casos mais complexos, poderá ter de dividir o seu script U-SQL numa sequência de Spark e outros passos implementados com funções azure Batch ou Azure.

Além disso, o Azure Data Lake Analytics oferece u-SQL em um ambiente de serviço de trabalho sem servidores, enquanto tanto os Azure Databricks como o Azure HDInsight oferecem spark em forma de serviço de cluster. Ao transformar a sua aplicação, terá de ter em conta as implicações de criar, dimensionar, escalar e desativar os clusters.

## <a name="transform-u-sql-scripts"></a>Transforme scripts U-SQL

Os scripts U-SQL seguem o seguinte padrão de processamento:

1. Os dados são lidos a partir de ficheiros não estruturados, utilizando a declaração, uma especificação de localização ou conjunto de `EXTRACT` ficheiros, bem como o extrator incorporado ou definido pelo utilizador e o esquema desejado, ou a partir de tabelas U-SQL (tabelas geridas ou externas). É representado como um conjunto de linhas.
2. Os rowsets são transformados em múltiplas declarações U-SQL que aplicam expressões U-SQL nos rowsets e produzem novos rowsets.
3. Finalmente, os conjuntos de linhas resultantes são saída sintetizadores em ambos os ficheiros utilizando a `OUTPUT` declaração que especifica a localização ou um outputter incorporado ou definido pelo utilizador, ou numa tabela U-SQL.

O script é avaliado preguiçosamente, o que significa que cada passo de extração e transformação é composto numa árvore de expressão e avaliado globalmente (o fluxo de dados).

Os programas de faíscas são semelhantes na medida em que utilizaria conectores Spark para ler os dados e criar os dataframes, depois aplicar as transformações nos quadros de dados usando o DSL ou o SparkSQL semelhantes a LINQ, e depois escrever o resultado em ficheiros, tabelas temporárias de Spark, alguns tipos de linguagem de programação ou na consola.

## <a name="transform-net-code"></a>Transforme o código .NET

A linguagem de expressão da U-SQL é C# e oferece uma variedade de maneiras de escalar o código personalizado .NET.

Uma vez que a Spark não suporta nativamente a execução do código .NET, terá de reescrever as suas expressões numa expressão equivalente de Spark, Scala, Java ou Python ou encontrar uma maneira de ligar para o seu código .NET. Se o seu script utilizar as bibliotecas .NET, tem as seguintes opções:

- Traduza o seu código .NET em Scala ou Python.
- Divida o seu script U-SQL em vários passos, onde utiliza os processos do Lote Azure para aplicar as transformações .NET (se conseguir uma escala aceitável)
- Utilize uma ligação de idioma .NET disponível na Open Source chamada Moebius. Este projeto não está num estado apoiado.

Em todo o caso, se tiver uma grande quantidade de lógica .NET nos seus scripts U-SQL, contacte-nos através do seu representante da Conta Microsoft para obter mais orientações.

Os seguintes detalhes são para os diferentes casos de utilizações .NET e C# em scripts U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transforme expressões de linha de linha u-SQL C# escalar

A linguagem de expressão da U-SQL é C#. Muitas das expressões de Linha U-SQL são implementadas de forma nativa para um melhor desempenho, enquanto expressões mais complexas podem ser executadas através da chamada para o quadro .NET.

A Spark tem a sua própria linguagem de expressão escalar (seja como parte do DSL ou no SparkSQL) e permite chamar para funções definidas pelo utilizador escritas na sua língua de hospedagem.

Se tiver expressões escalar em U-SQL, primeiro deve encontrar a expressão mais adequada nativamente compreendida para obter o maior desempenho, e depois mapear as outras expressões numa função definida pelo utilizador da linguagem de hospedagem spark da sua escolha.

Esteja ciente de que .NET e C# têm semântica de tipo diferente do que as linguagens de hospedagem de Spark e DSL de Spark. Veja [abaixo](#transform-typed-values) mais detalhes sobre as diferenças do sistema de tipo.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transforme funções scalar .NET definidas pelo utilizador e agregadores definidos pelo utilizador

A U-SQL fornece formas de ligar para funções arbitrárias de escalar .NET e de chamar agregadores definidos pelo utilizador escritos em .NET.

A Spark também oferece suporte para funções definidas pelo utilizador e agregadores definidos pelo utilizador escritos na maioria dos seus idiomas de hospedagem que podem ser chamados a partir do DSL e SparkSQL da Spark.

### <a name="transform-user-defined-operators-udos"></a>Transformar operadores definidos pelo utilizador (UDOs)

A U-SQL fornece várias categorias de operadores definidos pelo utilizador (UDOs) tais como extratores, outputters, redutores, processadores, aplicadores e combinadores que podem ser escritos em .NET (e - em certa medida - em Python e R).

A Spark não oferece o mesmo modelo de exsensibilidade para os operadores, mas tem capacidades equivalentes para alguns.

A Faísca equivalente a extratores e saídas são conectores Spark. Para muitos extratores U-SQL, você pode encontrar um conector equivalente na comunidade Spark. Para outros, terá de escrever um conector personalizado. Se o extrator U-SQL for complexo e utilizar várias bibliotecas .NET, pode ser preferível construir um conector em Scala que utilize interop para chamar para a biblioteca .NET que faz o processamento real dos dados. Nesse caso, terá de implantar o tempo de execução do Núcleo .NET para o cluster Spark e certificar-se de que as bibliotecas .NET referenciadas são compatíveis com a .NET Standard 2.0.

Os outros tipos de U-SQL UDOs terão de ser reescritos utilizando funções e agregadores definidos pelo utilizador e a expressão semanticamente apropriada de Spark DLS ou SparkSQL. Por exemplo, um processador pode ser mapeado para um SELECT de uma variedade de invocações udf, embalados como uma função que toma um dataframe como argumento e devolve um dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Transforme as bibliotecas opcionais da U-SQL

A U-SQL fornece um conjunto de bibliotecas opcionais e dedemonstração que oferecem [Python](data-lake-analytics-u-sql-python-extensions.md), [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, suporte AVRO,](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e [algumas capacidades de serviços cognitivos.](data-lake-analytics-u-sql-cognitive.md)

A Spark oferece a sua própria integração Python e R, pySpark e SparkR, respectivamente, e fornece conectores para ler e escrever JSON, XML e AVRO.

Se precisar de transformar um guião que se refere às bibliotecas de serviços cognitivos, recomendamos que nos contacte através do seu representante da Conta Microsoft.

## <a name="transform-typed-values"></a>Transformar valores digitados

Uma vez que o sistema de tipo U-SQL é baseado no sistema de tipo .NET e a Spark tem o seu próprio sistema de tipo, que é impactado pela ligação da língua anfitriã, terá de se certificar de que os tipos em que está a operar estão próximos e, para determinados tipos, as gamas de tipo, precisão e/ou escala podem ser ligeiramente diferentes. Além disso, a U-SQL e a Spark tratam `null` os valores de forma diferente.

### <a name="data-types"></a>Tipos de dados

A tabela seguinte dá os tipos equivalentes em Spark, Scala e PySpark para os tipos U-SQL dado.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Para obter mais informações, consulte:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Tipos de SQL e DataFrames de Faísca](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Tipos de valor scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Tratamento de NU

Em Spark, os tipos por padrão permitem valores NULOS enquanto em U-SQL, marca explicitamente o escalar, não objeto como nulidaível. Embora a Spark lhe permita definir uma coluna como não nulidade, não imporá a restrição e [poderá levar a um resultado errado](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Em Spark, null indica que o valor é desconhecido. Um valor NULO de Faísca é diferente de qualquer valor, incluindo a si mesmo. Comparações entre dois valores Nulos de Faísca, ou entre um valor NULO e qualquer outro valor, retorno desconhecido porque o valor de cada NULO é desconhecido.  

Este comportamento é diferente do U-SQL, que segue a semântica C# onde `null` é diferente de qualquer valor, mas igual a si mesmo.  

Assim, uma declaração da SparkSQL `SELECT` que utiliza `WHERE column_name = NULL` devoluções zero linhas mesmo que existam valores NULOs `column_name` em , enquanto em U-SQL, devolveria as linhas onde `column_name` está definida para `null` . Da mesma forma, uma declaração de Faísca `SELECT` que utiliza `WHERE column_name != NULL` devoluções zero linhas mesmo que existam valores não nulos `column_name` em , enquanto na U-SQL, devolveria as linhas que não têm nulos. Assim, se pretender a semântica de verificação nula U-SQL, deve utilizar [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) respectivamente (ou o seu equivalente DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transforme objetos de catálogo U-SQL

Uma grande diferença é que os Scripts U-SQL podem fazer uso dos seus objetos de catálogo, muitos dos quais não têm equivalente spark direto.

A Spark fornece suporte para os conceitos da loja Hive Meta, principalmente bases de dados e tabelas, para que possa mapear bases de dados u-SQL e schemas para bases de dados da Hive, e tabelas U-SQL para tabelas Spark (ver [dados móveis armazenados em tabelas U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)mas não tem suporte para vistas, funções de valor de mesa (TVFs), procedimentos armazenados, conjuntos U-SQL, fontes externas.

Os objetos de código U-SQL, tais como vistas, TVFs, procedimentos armazenados e conjuntos podem ser modelados através de funções de código e bibliotecas em Spark e referenciados utilizando a função da língua anfitriã e mecanismos de abstração processual (por exemplo, através da importação de módulos Python ou referenciando funções Scala).

Se o catálogo U-SQL tiver sido utilizado para partilhar objetos de dados e códigos entre projetos e equipas, então devem ser utilizados mecanismos equivalentes para a partilha (por exemplo, Maven para partilhar objetos de código).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transforme expressões de rowset U-SQL e expressões escalar baseadas em SQL

A linguagem central da U-SQL está a transformar rowsets e baseia-se no SQL. Segue-se uma lista não exaustiva das expressões mais comuns oferecidas em U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Agregados+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`expressões
- `CROSS`/`OUTER``APPLY`expressões
- `PIVOT`/`UNPIVOT`expressões
- `VALUES`construtor de rowset

- Definir expressões`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Além disso, a U-SQL fornece uma variedade de expressões escalar baseadas em SQL, tais como

- `OVER`expressões de janela
- uma variedade de agregadores incorporados e funções de classificação `SUM` (, `FIRST` etc.)
- Algumas das expressões mais conhecidas do SQL: `CASE` , , ( ), `LIKE` `NOT` `IN` `AND` `OR` etc.

A Spark oferece expressões equivalentes tanto na sua forma DSL como sparkSQL para a maioria destas expressões. Algumas das expressões não suportadas nativamente em Spark terão de ser reescritas usando uma combinação das expressões nativas da Faísca e padrões semânticamente equivalentes. Por exemplo, terá de `OUTER UNION` ser traduzido na combinação equivalente de projeções e sindicatos.

Devido à diferente manipulação dos valores NULOs, uma adesão u-SQL corresponderá sempre a uma linha se ambas as colunas que estão a ser comparadas contiverem um valor NULO, enquanto uma adesão em Spark não corresponderá a essas colunas a menos que sejam adicionados controlos nulos explícitos.

## <a name="transform-other-u-sql-concepts"></a>Transforme outros conceitos U-SQL

A U-SQL também oferece uma variedade de outras funcionalidades e conceitos, tais como consultas federadas contra bases de dados do SQL Server, parâmetros, variáveis de expressão escalar e lambda, variáveis do sistema, `OPTION` dicas.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Consultas federadas contra bases de dados/tabelas externas do Servidor SQL

A U-SQL fornece fonte de dados e tabelas externas, bem como consultas diretas contra a Base de Dados Azure SQL. Embora a Spark não ofereça as mesmas abstrações de objetos, fornece conector Spark para base de [dados Azure SQL](../azure-sql/database/spark-connector.md) que pode ser usado para consultar bases de dados SQL.

### <a name="u-sql-parameters-and-variables"></a>Parâmetros e variáveis U-SQL

Os parâmetros e as variáveis dos utilizadores têm conceitos equivalentes em Spark e nas suas línguas de hospedagem.

Por exemplo, em Scala, pode definir uma variável com a `var` palavra-chave:

```
var x = 2 * 3;
println(x)
```

As variáveis do sistema u-SQL (variáveis a começar) `@@` podem ser divididas em duas categorias:

- Variáveis do sistema settable que podem ser definidas para valores específicos para impactar o comportamento dos scripts
- Variáveis do sistema informativo que inquirem sistema e informação de nível de trabalho

A maioria das variáveis do sistema settable não têm equivalente direto em Spark. Algumas das variáveis do sistema informativo podem ser modeladas através da aprovação da informação como argumentos durante a execução do emprego, outras podem ter uma função equivalente na linguagem de hospedagem de Spark.

### <a name="u-sql-hints"></a>Dicas U-SQL

A U-SQL oferece várias formas sintáticas de fornecer dicas ao optimizador de consultas e ao motor de execução:  

- Definição de uma variável do sistema U-SQL
- uma `OPTION` cláusula associada à expressão rowset para fornecer uma dica de dados ou plano
- uma dica de adesão na sintaxe da expressão de união (por exemplo, `BROADCASTLEFT` )

O optimizador de consulta baseado em custos da Spark tem as suas próprias capacidades para fornecer dicas e afinar o desempenho da consulta. Consulte a documentação correspondente.

## <a name="next-steps"></a>Próximos passos

- [Compreender os formatos de dados da Spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Atualize as suas soluções de análise de big data do Azure Data Lake Storage Gen1 para O Armazenamento de Lagos Azure Data Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Transforme dados utilizando a atividade da Spark na Fábrica de Dados Azure](../data-factory/transform-data-using-spark.md)
- [Transforme dados utilizando a atividade da Urticária Hadoop na Fábrica de Dados Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
