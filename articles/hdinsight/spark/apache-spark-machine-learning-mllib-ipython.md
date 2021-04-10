---
title: Exemplo de aprendizagem automática com Spark MLlib em HDInsight - Azure
description: Aprenda a usar o Spark MLlib para criar uma aplicação de machine learning que analisa um conjunto de dados usando a classificação através da regressão logística.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: 224ebae58a5688f81d1b64b4a9c81d5abd8a9690
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868856"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Use Apache Spark MLlib para construir uma aplicação de machine learning e analisar um conjunto de dados

Aprenda a usar o Apache Spark MLlib para criar uma aplicação de machine learning. A aplicação fará análise preditiva num conjunto de dados aberto. Das bibliotecas de aprendizagem automática incorporadas da Spark, este exemplo usa *a classificação* através da regressão logística.

MLlib é uma biblioteca core Spark que fornece muitos utilitários úteis para tarefas de machine learning, tais como:

* Classificação
* Regressão
* Clustering
* Modelação
* Decomposição de valor singular (SVD) e análise principal de componentes (APC)
* Teste de hipóteses e cálculo das estatísticas da amostra

## <a name="understand-classification-and-logistic-regression"></a>Compreender classificação e regressão logística

*Classificação*, uma tarefa popular de aprendizagem automática, é o processo de triagem de dados de entrada em categorias. É o trabalho de um algoritmo de classificação descobrir como atribuir "rótulos" aos dados de entrada que fornece. Por exemplo, pode pensar num algoritmo de aprendizagem automática que aceita informações de stock como entrada. Em seguida, divide as ações em duas categorias: ações que deve vender e ações que deve manter.

A regressão logística é o algoritmo que se usa para a classificação. A API de regressão logística da Spark é útil para *a classificação binária,* ou para classificar dados de entrada em um de dois grupos. Para obter mais informações sobre regressões logísticas, consulte [a Wikipédia.](https://en.wikipedia.org/wiki/Logistic_regression)

Em resumo, o processo de regressão logística produz uma *função logística.* Use a função para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de análise preditiva sobre os dados da inspeção alimentar

Neste exemplo, você usa Spark para fazer algumas análises preditivas sobre os dados da inspeção alimentar **(Food_Inspections1.csv).** Dados adquiridos através do [portal de dados da Cidade de Chicago.](https://data.cityofchicago.org/) Este conjunto de dados contém informações sobre inspeções de estabelecimentos alimentares que foram realizadas em Chicago. Incluindo informações sobre cada estabelecimento, as violações encontradas (se houver), e os resultados da inspeção. O ficheiro de dados CSV já está disponível na conta de armazenamento associada ao cluster em **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nos passos abaixo, desenvolve-se um modelo para ver o que é preciso para passar ou falhar uma inspeção alimentar.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie uma app de aprendizagem automática Apache Spark MLlib

1. Crie um Caderno Jupyter utilizando o núcleo PySpark. Para obter as instruções, consulte [Criar um ficheiro de caderno Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

2. Importe os tipos necessários para este pedido. Copie e cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Por causa do núcleo PySpark, não precisa de criar nenhum contexto explicitamente. Os contextos Spark e Hive são criados automaticamente quando se executam a primeira célula de código.

## <a name="construct-the-input-dataframe"></a>Construa o dataframe de entrada

Utilize o contexto Spark para puxar os dados CSV crus para a memória como texto não estruturado. Em seguida, use a biblioteca CSV de Python para analisar cada linha dos dados.

1. Executar as seguintes linhas para criar um Conjunto de Dados Distribuídos Resiliente (RDD) importando e analisando os dados de entrada.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Execute o seguinte código para recuperar uma linha do RDD, para que possa ver o esquema de dados:

    ```PySpark
    inspections.take(1)
    ```

    O resultado é:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    A saída dá-lhe uma ideia do esquema do ficheiro de entrada. Inclui o nome de cada estabelecimento, e o tipo de estabelecimento. Além disso, o endereço, os dados das inspeções, e a localização, entre outras coisas.

3. Execute o seguinte código para criar um dataframe *(df*) e uma tabela temporária *(CondeResults*) com algumas colunas que são úteis para a análise preditiva. `sqlContext` é usado para fazer transformações em dados estruturados.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    As quatro colunas de interesse no dataframe são **identificação,** **nome,** **resultados** e **violações.**

4. Executar o seguinte código para obter uma pequena amostra dos dados:

    ```PySpark
    df.show(5)
    ```

    O resultado é:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Compreender os dados

Vamos começar a ter uma noção do que o conjunto de dados contém. 

1. Executar o seguinte código para mostrar os valores distintos na coluna **de resultados:**

    ```PySpark
    df.select('results').distinct().show()
    ```

    O resultado é:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Executar o seguinte código para visualizar a distribuição destes resultados:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    A `%%sql` magia seguida garante que a saída da consulta é `-o countResultsdf` persistiu localmente no servidor Jupyter (tipicamente o headnode do cluster). A saída é persistiu como um dataframe [de pandas](https://pandas.pydata.org/) com o nome especificado **CountResultsdf**. Para obter mais informações sobre a `%%sql` magia e outras magias disponíveis com o kernel PySpark, consulte [Kernels disponível em Cadernos Jupyter com aglomerados Apache Spark HDInsight.](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)

    O resultado é:

    :::image type="content" source="./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png " alt-text="Saída de consulta SQL" border="true":::

3. Também pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados, para criar um enredo. Como o enredo deve ser criado a partir do conjunto de **dados de Contagem Localmente** PersistidoRes, o corte de código deve começar com a `%%local` magia. Esta ação garante que o código é executado localmente no servidor Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Para prever um resultado da inspeção alimentar, é preciso desenvolver um modelo baseado nas violações. Como a regressão logística é um método de classificação binária, faz sentido agrupar os dados dos resultados em duas categorias: **Fail** and **Pass**:

   - Aprovação
       - Aprovação
       - Passe c/ condições
   - Reprovado
       - Reprovado
   - Eliminar
       - Negócios não localizados
       - Fora do negócio

     Os dados com os outros resultados ("Business Not Located" ou "out of Business") não são úteis, e compõem uma pequena percentagem dos resultados de qualquer forma.

4. Execute o seguinte código para converter o dados existente `df` num novo dataframe onde cada inspeção é representada como um par de violações de etiquetas. Neste caso, um rótulo de `0.0` falha representa um fracasso, um rótulo de `1.0` representa um sucesso, e um rótulo de `-1.0` representa alguns resultados para além desses dois resultados.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Executar o seguinte código para mostrar uma linha dos dados rotulados:

    ```PySpark
    labeledData.take(1)
    ```

    O resultado é:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística a partir do dataframe de entrada

A tarefa final é converter os dados rotulados. Converter os dados num formato que pode ser analisado por regressão logística. A entrada para um algoritmo de regressão logística precisa de um conjunto de pares de *vetores de característica de etiqueta.* Onde o "vetor de recurso" é um vetor de números que representam o ponto de entrada. Por isso, é necessário converter a coluna "violações", que é semi-estruturada e contém muitos comentários em texto livre. Converta a coluna numa série de números reais que uma máquina poderia facilmente compreender.

Uma abordagem padrão de aprendizagem automática para o processamento da linguagem natural é atribuir a cada palavra distinta um "índice". Em seguida, passe um vetor para o algoritmo de aprendizagem automática. De tal forma que o valor de cada índice contenha a frequência relativa dessa palavra na cadeia de texto.

A MLlib fornece uma maneira fácil de fazer esta operação. Primeiro, "tokenize" cada corda de violações para obter as palavras individuais em cada corda. Em seguida, use um `HashingTF` para converter cada conjunto de fichas em um vetor de recurso que pode então ser passado para o algoritmo de regressão logística para construir um modelo. Conduz todos estes passos em sequência usando um "oleoduto".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Avaliar o modelo utilizando outro conjunto de dados

Pode usar o modelo que criou anteriormente para *prever* quais serão os resultados de novas inspeções. As previsões baseiam-se nas violações que foram observadas. Treinou este modelo no conjunto de dados **Food_Inspections1.csv.** Pode utilizar um segundo conjunto de dados, **Food_Inspections2.csv,** para *avaliar* a força deste modelo nos novos dados. Este segundo conjunto de dados **(Food_Inspections2.csv)** encontra-se no recipiente de armazenamento predefinido associado ao cluster.

1. Executar o seguinte código para criar um novo dataframe, **previsõesDf** que contém a previsão gerada pelo modelo. O snippet também cria uma tabela temporária chamada **Previsões** com base no dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Deve ver uma saída como o seguinte texto:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Olha para uma das previsões. Executar este corte:

    ```PySpark
    predictionsDf.take(1)
    ```

   Há uma previsão para a primeira entrada no conjunto de dados de teste.

1. O `model.transform()` método aplica a mesma transformação a quaisquer novos dados com o mesmo esquema, e chega a uma previsão de como classificar os dados. Pode-se fazer algumas estatísticas para ter uma noção de como as previsões eram:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A saída parece o seguinte texto:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Usar a regressão logística com a Spark dá-lhe um modelo da relação entre descrições de violações em inglês. E se um determinado negócio passaria ou falharia numa inspeção alimentar.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão

Pode agora construir uma visualização final para ajudá-lo a raciocinar sobre os resultados deste teste.

1. Começa-se por extrair as diferentes previsões e resultados da tabela temporária **de Previsões** criada anteriormente. As seguintes consultas separam a saída como *true_positive,* *false_positive,* *true_negative* e *false_negative.* Nas consultas abaixo, desativa a visualização utilizando `-q` e também guarda a saída (utilizando) como `-o` dataframes que podem ser usados com a `%%local` magia.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Finalmente, utilize o seguinte corte para gerar o enredo usando **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Deverá ver o seguinte resultado:

    :::image type="content" source="./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png " alt-text="Produção de aplicação de aprendizagem de máquinas de faísca - percentagens de gráfico de tortas de inspeções alimentares falhadas." border="true":::

    Neste gráfico, um resultado "positivo" refere-se à inspeção alimentar falhada, enquanto um resultado negativo refere-se a uma inspeção aprovada.

## <a name="shut-down-the-notebook"></a>Desligue o caderno

Depois de terminar a execução da aplicação, deve desligar o caderno para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Esta ação encerra e fecha o bloco de notas.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit modelo de aprendizagem profunda com Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
