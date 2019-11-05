---
title: Exemplo de aprendizado de máquina com Spark MLlib no HDInsight – Azure
description: Saiba como usar o Spark MLlib para criar um aplicativo de Machine Learning que analisa um conjunto de informações usando a classificação por meio de regressão logística.
keywords: Machine Learning do Spark, exemplo do Spark Machine Learning
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494084"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Use Apache Spark MLlib para criar um aplicativo de aprendizado de máquina e analisar um conjunto de informações

Saiba como usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar um aplicativo de aprendizado de máquina para realizar uma análise preditiva simples em um conjunto de informações aberto. A partir de bibliotecas de aprendizado de máquina internas do Spark, este exemplo usa *classificação* por meio de regressão logística. 

O MLlib é uma biblioteca principal do Spark que fornece muitos utilitários úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

* Classificação
* Regressão
* Clustering
* Modelagem de tópico
* Decomposição de valor singular (SVD) e análise de componente principal (PCA)
* Teste de hipótese e cálculo de estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Entenda a classificação e a regressão logística
*Classificação*, uma tarefa popular de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação descobrir como atribuir "rótulos" aos dados de entrada que você fornecer. Por exemplo, você pode considerar um algoritmo de aprendizado de máquina que aceita informações de ações como entrada e divide o estoque em duas categorias: ações que você deve vender e ações que devem ser mantidas.

A regressão logística é o algoritmo que você usa para classificação. A API de regressão logística do Spark é útil para *classificação binária*ou classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre regressões logísticas, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de análise preditiva nos dados de inspeção de alimentos
Neste exemplo, você usa o Spark para executar uma análise preditiva nos dados de inspeção de alimentos (**Food_Inspections1. csv**) que foi adquirido por meio do [portal de dados da cidade de Chicago](https://data.cityofchicago.org/). Esse conjunto de dados contém informações sobre as inspeções de estabelecimento de alimentos que foram conduzidas em Chicago, incluindo informações sobre cada estabelecimento, as violações encontradas (se houver) e os resultados da inspeção. O arquivo de dados CSV já está disponível na conta de armazenamento associada ao cluster em **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nas etapas a seguir, você desenvolverá um modelo para ver o que é necessário para passar ou reprovar uma inspeção de alimentos.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Criar um aplicativo de Machine Learning do Apache Spark MLlib

1. Crie um bloco de notas do Jupyter com o kernel de PySpark. Para obter as instruções, veja [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importe os tipos necessários para este aplicativo. Copie e cole o código a seguir em uma célula vazia e pressione **Shift + Enter**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Devido ao kernel do PySpark, você não precisa criar nenhum contexto explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código. 

## <a name="construct-the-input-dataframe"></a>Construir o quadro de dados de entrada

Como os dados brutos estão em um formato CSV, você pode usar o contexto do Spark para efetuar pull do arquivo na memória como texto não estruturado e, em seguida, usar a biblioteca CSV do Python para analisar cada linha dos dados.

1. Execute as linhas a seguir para criar um conjunto de dados distribuído resiliente (RDD) importando e analisando o dado de entrada.

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

2. Execute o código a seguir para recuperar uma linha do RDD, para que você possa obter uma aparência do esquema de dados:

    ```PySpark
    inspections.take(1)
    ```

    A saída é:

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

    A saída lhe dá uma ideia do esquema do arquivo de entrada. Ele inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados das inspeções e o local, entre outras coisas. 

3. Execute o código a seguir para criar um dataframe (*DF*) e uma tabela temporária (*CountResults*) com algumas colunas que são úteis para a análise preditiva. `sqlContext` é usado para executar transformações em dados estruturados. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    As quatro colunas de interesse no dataframe são **ID**, **nome**, **resultados**e **violações**.

4. Execute o código a seguir para obter uma pequena amostra dos dados:

    ```PySpark
    df.show(5)
    ```

    A saída é:

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

## <a name="understand-the-data"></a>Entender os dados

Vamos começar a entender o que o conjunto de conteúdo contém. 

1. Execute o seguinte código para mostrar os valores distintos na coluna **resultados** :

    ```PySpark
    df.select('results').distinct().show()
    ```

    A saída é:

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

2. Execute o seguinte código para visualizar a distribuição desses resultados:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    A `%%sql` mágica seguida por `-o countResultsdf` garante que a saída da consulta seja persistida localmente no servidor Jupyter (normalmente, o cabeçalho do cluster). A saída é persistida como um dataframe do [pandas](https://pandas.pydata.org/) com o nome especificado **countResultsdf**. Para obter mais informações sobre a mágica `%%sql` e outras mágicas disponíveis com o kernel PySpark, confira [kernels disponíveis em notebooks Jupyter com clusters HDInsight Apache Spark](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    A saída é:

    ![Saída da consulta SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Saída da consulta SQL")


3. Você também pode usar [matplotlib](https://en.wikipedia.org/wiki/Matplotlib), uma biblioteca usada para construir a visualização de dados, para criar uma plotagem. Como o gráfico deve ser criado a partir do dataframe **countResultsdf** persistente local, o trecho de código deve começar com a mágica `%%local`. Isso garante que o código seja executado localmente no servidor Jupyter.

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

    A saída é:

    ![Saída do aplicativo Spark Machine Learning-gráfico de pizza com cinco resultados de inspeção distintos](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Saída de resultado do Machine Learning do Spark")

    Para prever um resultado de inspeção de alimentos, você precisa desenvolver um modelo com base nas violações. Como a regressão logística é um método de classificação binária, faz sentido agrupar os dados resultantes em duas categorias: **falha** e **aprovação**:

   - Passá
       - Passá
       - Pass w/condições
   - Recuperação
       - Recuperação
   - Carte
       - Empresa não localizada
       - Fora de negócios

     Os dados com os outros resultados ("negócios não localizados" ou "fora de negócios") não são úteis e formam um percentual muito pequeno dos resultados de qualquer forma.

4. Execute o código a seguir para converter o dataframe existente (`df`) em um novo dataframe em que cada inspeção é representada como um par de violações de rótulo. Nesse caso, um rótulo de `0.0` representa uma falha, um rótulo de `1.0` representa um êxito e um rótulo de `-1.0` representa alguns resultados além daqueles dois. 

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

5. Execute o código a seguir para mostrar uma linha dos dados rotulados:

    ```PySpark
    labeledData.take(1)
    ```

    A saída é:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística a partir do quadro de dados de entrada

A tarefa final é converter os dados rotulados em um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares de vetor de recurso de rótulo*, em que o "vetor de recurso" é um vetor de números que representa o ponto de entrada. Portanto, você precisa converter a coluna "violações", que é semiestruturada e contém muitos comentários em texto livre, para uma matriz de números reais que um computador pode entender facilmente.

Uma abordagem de aprendizado de máquina padrão para o processamento de linguagem natural é atribuir a cada palavra distinta um "índice" e, em seguida, passar um vetor para o algoritmo de aprendizado de máquina, de modo que o valor de cada índice contenha a frequência relativa dessa palavra na cadeia de texto.

O MLlib fornece uma maneira fácil de executar essa operação. Primeiro, "indexar" cada cadeia de caracteres de violações para obter as palavras individuais em cada cadeia de caracteres. Em seguida, use uma `HashingTF` para converter cada conjunto de tokens em um vetor de recurso que pode ser passado para o algoritmo regressão logística para construir um modelo. Você realiza todas essas etapas em sequência usando um "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Avaliar o modelo usando outro conjunto de conjuntos

Você pode usar o modelo criado anteriormente para *prever* quais serão os resultados das novas inspeções, com base nas violações observadas. Você treinou esse modelo no conjunto de **Food_Inspections1. csv**. Você pode usar um segundo conjunto de dados, **Food_Inspections2. csv**, para *avaliar* o nível de segurança desse modelo no novo dado. Esse segundo conjunto de dados (**Food_Inspections2. csv**) está no contêiner de armazenamento padrão associado ao cluster.

1. Execute o código a seguir para criar um novo dataframe, **predictionsDf** que contém a previsão gerada pelo modelo. O trecho de código também cria uma tabela temporária chamada **previsões** com base no dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Deve ver um resultado como o seguinte:

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

1. Examine uma das previsões. Execute este trecho de código:

    ```PySpark
    predictionsDf.take(1)
    ```

   Há uma previsão para a primeira entrada no conjunto de dados de teste.
1. O método `model.transform()` aplica a mesma transformação a quaisquer novos dados com o mesmo esquema e chega a uma previsão de como classificar os dados. Você pode fazer algumas estatísticas simples para ter uma noção de quão precisas foram as previsões:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A saída é semelhante ao seguinte:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    O uso da regressão logística com o Spark oferece um modelo preciso da relação entre as descrições de violações em inglês e se um determinado negócio seria aprovado ou reprovado em uma inspeção de alimentos.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão
Agora você pode construir uma visualização final para ajudá-lo sobre os resultados desse teste.

1. Você começa extraindo as diferentes previsões e os resultados da tabela temporária de **previsões** criada anteriormente. As consultas a seguir separam a saída como *true_positive*, *false_positive*, *true_negative*e *false_negative*. Nas consultas abaixo, você desativa a visualização usando `-q` e também salva a saída (usando `-o`) como quadros de molduras que podem ser usados com a mágica `%%local`.

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

1. Por fim, use o trecho a seguir para gerar a plotagem usando **matplotlib**.

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

    Deverá ver o resultado seguinte:

    ![Saída do aplicativo Spark Machine Learning – porcentagens do gráfico de pizza de inspeções de alimentos com falha.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Saída de resultado do Machine Learning do Spark")

    Neste gráfico, um resultado "positivo" refere-se à inspeção de alimentos com falha, enquanto um resultado negativo se refere a uma inspeção passada.

## <a name="shut-down-the-notebook"></a>Desligar o notebook
Depois de concluir a execução do aplicativo, você deve desligar o bloco de anotações para liberar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Isso desliga e fecha o bloco de anotações.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
