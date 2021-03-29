---
title: Compreenda os conceitos de código Apache Spark para desenvolvedores U-SQL do Azure Data Lake Analytics.
description: Este artigo descreve conceitos de Apache Spark para ajudar os desenvolvedores da U-SQL a entender os conceitos de código Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4faa48a9edb5ea157fde67e4a4f3008864342075
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639757"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Compreenda o código Apache Spark para desenvolvedores U-SQL

Esta secção fornece orientações de alto nível sobre a transformação de Scripts U-SQL para Apache Spark.

- Começa com uma [comparação dos paradigmas de processamento das duas línguas](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Fornece dicas sobre como:
   - [Transformar scripts](#transform-u-sql-scripts) incluindo [expressões](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) de linha u-SQL
   - [.NET código](#transform-net-code)
   - [Tipos de dados](#transform-typed-values)
   - [Objetos de catálogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Compreenda os paradigmas e paradigmas de processamento da u-SQL e do faísca

Antes de começar a migrar os scripts U-SQL do Azure Data Analytics para Spark, é útil compreender a linguagem geral e as filosofias de processamento dos dois sistemas.

U-SQL é uma linguagem de consulta declarativa semelhante ao SQL que utiliza um paradigma de fluxo de dados e permite-lhe incorporar e escalar facilmente o código de utilizador escrito em .NET (por exemplo C#), Python e R. As extensões do utilizador podem implementar expressões simples ou funções definidas pelo utilizador, mas também podem fornecer ao utilizador a capacidade de implementar os chamados operadores definidos pelo utilizador que implementam operadores personalizados para executar transformações de nível de linha, extrações e saída de escrita.

Spark é um quadro de escala que oferece várias ligações linguísticas em Scala, Java, Python, .NET, etc. onde você escreve principalmente o seu código em uma destas línguas, criar abstrações de dados chamadas conjuntos de dados distribuídos resilientes (RDD), dataframes e conjuntos de dados e, em seguida, usar uma linguagem específica de domínio linq (DSL) para transformá-los. Também fornece o SparkSQL como uma sublanguagem declarativa sobre o dataframe e as abstrações do conjunto de dados. O DSL fornece duas categorias de operações, transformações e ações. A aplicação de transformações nas abstrações de dados não executará a transformação, mas sim construirá o plano de execução que será submetido a avaliação com uma ação (por exemplo, escrever o resultado numa tabela ou arquivo temporário, ou imprimir o resultado).

Assim, ao traduzir um script U-SQL para um programa Spark, terá de decidir qual o idioma que pretende utilizar para, pelo menos, gerar a abstração do quadro de dados (que é atualmente a abstração de dados mais utilizada) e se pretende escrever as transformações declarativas do fluxo de dados utilizando o DSL ou o SparkSQL. Em alguns casos mais complexos, poderá ter de dividir o seu script U-SQL numa sequência de Spark e outros passos implementados com Azure Batch ou Azure Functions.

Além disso, o Azure Data Lake Analytics oferece u-SQL num ambiente de serviço de trabalho sem servidor, enquanto tanto a Azure Databricks como a Azure HDInsight oferecem a Spark em forma de serviço de cluster. Ao transformar a sua aplicação, terá de ter em conta as implicações de agora criar, dimensionar, escalar e desativar os clusters.

## <a name="transform-u-sql-scripts"></a>Transformar scripts U-SQL

Os scripts U-SQL seguem o seguinte padrão de processamento:

1. Os dados são lidos a partir de ficheiros não estruturados, utilizando a `EXTRACT` declaração, uma especificação de localização ou conjunto de ficheiros, e o extrator incorporado ou definido pelo utilizador e o esquema desejado, ou a partir de tabelas U-SQL (tabelas geridas ou externas). É representado como um conjunto de linhas.
2. Os conjuntos de linhas transformam-se em múltiplas declarações U-SQL que aplicam expressões U-SQL aos conjuntos de linhas e produzem novos conjuntos de linhas.
3. Finalmente, os conjuntos de linha resultantes são saídas em ambos os ficheiros usando a `OUTPUT` declaração que especifica a(s) localização e um outputter incorporado ou definido pelo utilizador, ou para uma tabela U-SQL.

O script é avaliado preguiçosamente, o que significa que cada passo de extração e transformação é composto em uma árvore de expressão e avaliado globalmente (o fluxo de dados).

Os programas spark são semelhantes na medida em que você usaria conectores Spark para ler os dados e criar os dataframes, em seguida, aplicar as transformações nos dataframes usando o DSL ou SparkSQL semelhante ao LINQ, e, em seguida, escrever o resultado em ficheiros, tabelas temporárias de faísca, alguns tipos de linguagem de programação, ou a consola.

## <a name="transform-net-code"></a>Transformar código .NET

A linguagem de expressão U-SQL é C# e oferece uma variedade de formas de escalar o código .NET personalizado.

Uma vez que a Spark não suporta a execução de código .NET de forma nativa, terá de reescrever as suas expressões numa expressão equivalente a Spark, Scala, Java ou Python ou encontrar uma forma de entrar no seu código .NET. Se o seu script utilizar bibliotecas .NET, tem as seguintes opções:

- Traduza o seu código .NET em Scala ou Python.
- Divida o seu script U-SQL em vários passos, onde utiliza os processos do Azure Batch para aplicar as transformações .NET (se conseguir uma escala aceitável)
- Utilize uma ligação linguística .NET disponível em Open Source chamada Moebius. Este projeto não está num estado apoiado.

Em qualquer caso, se tiver uma grande quantidade de lógica .NET nos seus scripts U-SQL, contacte-nos através do seu representante da Conta microsoft para obter mais orientações.

Os seguintes detalhes são para os diferentes casos de utilizações .NET e C# em scripts U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transforme expressões scalar inline U-SQL C#

A linguagem de expressão da U-SQL é C#. Muitas das expressões U-SQL de linha scalar são implementadas de forma nativa para um melhor desempenho, enquanto expressões mais complexas podem ser executadas através da chamada para o quadro .NET.

A Spark tem a sua própria linguagem de expressão escalar (quer como parte do DSL, quer no SparkSQL) e permite chamar para funções definidas pelo utilizador escritas no seu idioma de hospedagem.

Se tiver expressões escalares em U-SQL, deve primeiro encontrar a expressão de escala de faísca mais adequada para obter o maior desempenho e, em seguida, mapear as outras expressões numa função definida pelo utilizador da linguagem de hospedagem Spark da sua escolha.

Esteja ciente de que .NET e C# têm semântica tipo diferente das línguas de anfitrião Spark e DSL de Spark. Veja [abaixo](#transform-typed-values) mais detalhes sobre as diferenças do sistema do tipo.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transforme as funções scalar .NET definidas pelo utilizador e os agregadores definidos pelo utilizador

O U-SQL fornece formas de chamar funções de escalar arbitrário .NET e de chamar agregadores definidos pelo utilizador escritos em .NET.

A Spark também oferece suporte para funções definidas pelo utilizador e agregadores definidos pelo utilizador escritos na maioria dos seus idiomas de hospedagem que podem ser chamados a partir do DSL e SparkSQL da Spark.

### <a name="transform-user-defined-operators-udos"></a>Transformar operadores definidos pelo utilizador (UDOs)

A U-SQL fornece várias categorias de operadores definidos pelo utilizador (UDOs) tais como extratores, outputters, redutores, processadores, aplicadores e combinadores que podem ser escritos em .NET (e - em certa medida - em Python e R).

A Spark não oferece o mesmo modelo de extensibilidade para os operadores, mas tem capacidades equivalentes para alguns.

O spark equivalente aos extratores e saídadores são conectores Spark. Para muitos extratores U-SQL, você pode encontrar um conector equivalente na comunidade Spark. Para outros, terá de escrever um conector personalizado. Se o extrator U-SQL for complexo e utilizar várias bibliotecas .NET, poderá ser preferível construir um conector em Scala que utilize o interop para chamar para a biblioteca .NET que faz o processamento real dos dados. Nesse caso, terá de implantar o tempo de funcionação .NET Core no cluster Spark e certificar-se de que as bibliotecas .NET referenciadas estão em conformidade com a Norma .NET 2.0.

Os outros tipos de U-SQL UDOs terão de ser reescritos utilizando funções e agregadores definidos pelo utilizador e a expressão semântica de Spark DLS ou SparkSQL. Por exemplo, um processador pode ser mapeado para um SELECT de uma variedade de invocações UDF, embalados como uma função que toma um dataframe como argumento e devolve um dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Transformar as bibliotecas opcionais da U-SQL

A U-SQL disponibiliza um conjunto de bibliotecas opcionais e de demonstração que oferecem [python,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, suporte AVRO,](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e [algumas capacidades de serviços cognitivos.](data-lake-analytics-u-sql-cognitive.md)

A Spark oferece a sua própria integração Python e R, pySpark e SparkR, respectivamente, e fornece conectores para ler e escrever JSON, XML e AVRO.

Se precisar de transformar um script referente às bibliotecas de serviços cognitivos, recomendamos que nos contacte através do seu representante da Conta Microsoft.

## <a name="transform-typed-values"></a>Transformar valores dactilografado

Uma vez que o sistema tipo U-SQL se baseia no sistema tipo .NET e o Spark tem o seu próprio sistema tipo, que é impactado pela ligação linguística do anfitrião, terá de se certificar de que os tipos em que está a operar são próximos e, para certos tipos, as gamas de tipo, precisão e/ou escala podem ser ligeiramente diferentes. Além disso, u-SQL e Spark tratam `null` os valores de forma diferente.

### <a name="data-types"></a>Tipos de dados

A tabela seguinte dá os tipos equivalentes em Spark, Scala e PySpark para os tipos U-SQL.

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
- [Tipos de SQL e DataFrames spark](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Tipos de valor scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.tipos](https://spark.apache.org/docs/2.3.1/api/python/_modules/pyspark/sql/types.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Tratamento de NU

Em Spark, os tipos por predefinição permitem valores NULOS enquanto em U-SQL, você marca explicitamente o scalar, não-objeto como anulado. Embora a Spark permita definir uma coluna como não anulação, não aplicará a restrição e [poderá conduzir a um resultado errado](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Em Spark, NULL indica que o valor é desconhecido. Um valor DE ANULAção de Faísca é diferente de qualquer valor, incluindo a si mesmo. As comparações entre dois valores de NULO de Faísca, ou entre um valor NULO e qualquer outro valor, devolvem desconhecido porque o valor de cada NUNão é desconhecido.  

Este comportamento é diferente do U-SQL, que segue a semântica C# onde `null` é diferente de qualquer valor, mas igual a si mesmo.  

Assim, uma declaração da SparkSQL `SELECT` que utiliza `WHERE column_name = NULL` retorna zero linhas mesmo que existam valores NULOS `column_name` em, enquanto em U-SQL, devolveria as linhas onde `column_name` está definida `null` . Da mesma forma, uma declaração de Spark `SELECT` que usa `WHERE column_name != NULL` retorna zero linhas mesmo que existam valores não nulos em `column_name` , enquanto em U-SQL, devolveria as linhas que não têm nulo. Assim, se quiser a semântica de verificação de nulos U-SQL, deve utilizar [isull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) respectivamente (ou o seu equivalente DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transformar objetos de catálogo U-SQL

Uma grande diferença é que os Scripts U-SQL podem fazer uso dos seus objetos de catálogo, muitos dos quais não têm equivalente spark direto.

A Spark fornece suporte para os conceitos de loja Hive Meta, principalmente bases de dados e tabelas, para que possa mapear bases de dados e esquemas U-SQL para bases de dados de Colmeia, e tabelas U-SQL para tabelas Spark (ver [dados móveis armazenados em tabelas U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)mas não tem suporte para visualizações, funções de valor de tabela (TVFs), procedimentos armazenados, conjuntos U-SQL, fontes de dados externas, etc.

Os objetos de código U-SQL, tais como pontos de vista, TVFs, procedimentos armazenados e conjuntos podem ser modelados através de funções de código e bibliotecas em Spark e referenciados usando a função da língua anfitriã e mecanismos de abstração processual (por exemplo, através da importação de módulos Python ou referenciação de funções scala).

Se o catálogo U-SQL tiver sido utilizado para partilhar dados e códigos de objetos entre projetos e equipas, então devem ser utilizados mecanismos equivalentes de partilha (por exemplo, Maven para a partilha de objetos de código).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transformar expressões de linha U-SQL e expressões escalar baseadas em SQL

A linguagem principal da U-SQL está a transformar os conjuntos de linhas e é baseada em SQL. Segue-se uma lista não exaustiva das expressões de linha mais comuns oferecidas em U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Agregados+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`expressões
- `CROSS`/`OUTER``APPLY`expressões
- `PIVOT`/`UNPIVOT` expressões
- `VALUES` construtor de conjunto de linha

- Definir expressões `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Além disso, o U-SQL fornece uma variedade de expressões escalar baseadas em SQL, tais como

- `OVER` expressões de janela
- uma variedade de agregadores embutidos e funções de classificação `SUM` (, `FIRST` etc.)
- Algumas das expressões de sql mais familiares: `CASE` , , ( ) , `LIKE` `NOT` `IN` `AND` `OR` etc.

A Spark oferece expressões equivalentes na sua forma DSL e SparkSQL para a maioria destas expressões. Algumas das expressões não suportadas nativamente em Spark terão de ser reescritas usando uma combinação das expressões nativas de Faísca e padrões semânticamente equivalentes. Por exemplo, `OUTER UNION` terá de ser traduzido para a combinação equivalente de projeções e sindicatos.

Devido ao manuseamento diferente dos valores NULOS, uma junção U-SQL corresponderá sempre a uma linha se ambas as colunas que estão a ser comparadas contiverem um valor NUDO, enquanto uma junção em Spark não corresponderá a essas colunas a menos que sejam adicionados controlos nulos explícitos.

## <a name="transform-other-u-sql-concepts"></a>Transforme outros conceitos U-SQL

O U-SQL também oferece uma variedade de outras funcionalidades e conceitos, tais como consultas federadas contra bases de dados do SQL Server, parâmetros, variáveis de expressão de scalar e lambda, variáveis do sistema, `OPTION` dicas.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Consultas federadas contra bases de dados/tabelas externas do SQL Server

A U-SQL fornece fonte de dados e tabelas externas, bem como consultas diretas contra a Base de Dados Azure SQL. Embora a Spark não ofereça as mesmas abstrações de objetos, fornece [conector Spark para Azure SQL Database](../azure-sql/database/spark-connector.md) que pode ser usado para consultar bases de dados SQL.

### <a name="u-sql-parameters-and-variables"></a>Parâmetros e variáveis U-SQL

Parâmetros e variáveis de utilizador têm conceitos equivalentes em Spark e suas línguas de hospedagem.

Por exemplo, em Scala, pode definir uma variável com a `var` palavra-chave:

```
var x = 2 * 3;
println(x)
```

As variáveis do sistema U-SQL (variáveis a `@@` começar) podem ser divididas em duas categorias:

- Variáveis de sistema de definição que podem ser definidas para valores específicos para impactar o comportamento dos scripts
- Variáveis do sistema informativo que inquirem o sistema e a informação do nível de emprego

A maioria das variáveis do sistema de definição não têm equivalente direto em Spark. Algumas das variáveis do sistema informativo podem ser modeladas passando a informação como argumentos durante a execução do trabalho, outras podem ter uma função equivalente na linguagem de hospedagem de Spark.

### <a name="u-sql-hints"></a>Pistas U-SQL

O U-SQL oferece várias formas sintáticas de fornecer dicas para o otimizador de consultas e motor de execução:  

- Definição de uma variável do sistema U-SQL
- uma `OPTION` cláusula associada à expressão rowset para fornecer um dado ou dica de plano
- uma dica de união na sintaxe da expressão de união (por exemplo, `BROADCASTLEFT` )

O otimizador de consultas baseado em custos da Spark tem as suas próprias capacidades para fornecer dicas e afinar o desempenho da consulta. Consulte a documentação correspondente.

## <a name="next-steps"></a>Passos seguintes

- [Compreender os formatos de dados da Spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [.NET para Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Atualize as suas soluções de análise de big data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Transformar dados usando a atividade da Spark na Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade da Colmeia Hadoop na Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
