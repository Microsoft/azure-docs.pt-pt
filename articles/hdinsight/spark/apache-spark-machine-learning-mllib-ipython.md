---
title: Exemplo de aprendizagem automática com Spark MLlib no HDInsight - Azure
description: Aprenda a usar o Spark MLlib para criar uma app de machine learning que analise um conjunto de dados usando a classificação através da regressão logística.
keywords: spark machine learning, exemplo de aprendizagem de máquina de faísca
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494084"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Use Apache Spark MLlib para construir uma aplicação de machine learning e analisar um conjunto de dados

Aprenda a usar Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar uma aplicação de machine learning para fazer uma análise preditiva simples num conjunto de dados aberto. Das bibliotecas de machine learning incorporadas da Spark, este exemplo utiliza a *classificação* através da regressão logística. 

MLlib é uma biblioteca core Spark que fornece muitos utilitários úteis para tarefas de aprendizagem automática, incluindo utilitários adequados para:

* Classificação
* Regressão
* Clustering
* Modelação de tópicos
* Decomposição de valor singular (SVD) e análise principal de componentes (PCA)
* Ensaio de hipóteses e cálculo das estatísticas da amostra

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e a regressão logística
*A classificação*, uma tarefa popular de aprendizagem automática, é o processo de classificação dos dados de entrada em categorias. É trabalho de um algoritmo de classificação descobrir como atribuir "rótulos" aos dados de entrada que fornece. Por exemplo, pode pensar num algoritmo de aprendizagem automática que aceite a informação de stock como entrada e divide as ações em duas categorias: ações que deve vender e ações que deve manter.

Regressão logística é o algoritmo que se usa para a classificação. A API de regressão logística da Spark é útil para a *classificação binária,* ou classificando os dados de entrada em um de dois grupos. Para obter mais informações sobre regressões logísticas, consulte a [Wikipédia.](https://en.wikipedia.org/wiki/Logistic_regression)

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou ao outro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de análise preditiva sobre dados de inspeção alimentar
Neste exemplo, você usa spark para realizar algumas análises preditivas sobre dados de inspeção alimentar **(Food_Inspections1.csv**) que foi adquirido através do [portal de dados da cidade de Chicago](https://data.cityofchicago.org/). Este conjunto de dados contém informações sobre inspeções de estabelecimentos alimentares que foram realizadas em Chicago, incluindo informações sobre cada estabelecimento, as violações encontradas (se houver), e os resultados da inspeção. O ficheiro de dados CSV já está disponível na conta de armazenamento associada ao cluster em **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nos degraus abaixo, desenvolve-se um modelo para ver o que é preciso para passar ou falhar numa inspeção alimentar.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie uma app de aprendizagem automática Apache Spark MLlib

1. Crie um bloco de notas do Jupyter com o kernel de PySpark. Para obter as instruções, veja [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importar os tipos necessários para este pedido. Copie e cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Por causa do kernel PySpark, você não precisa criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código. 

## <a name="construct-the-input-dataframe"></a>Construir o quadro de dados de entrada

Como os dados brutos estão num formato CSV, pode utilizar o contexto Spark para puxar o ficheiro para a memória como texto não estruturado e, em seguida, usar a biblioteca CSV da Python para analisar cada linha dos dados.

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

2. Executar o seguinte código para recuperar uma linha do RDD, para que possa ver o esquema de dados:

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

    A saída dá-lhe uma ideia do esquema do ficheiro de entrada. Inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados das inspeções e a localização, entre outras coisas. 

3. Executar o seguinte código para criar um dataframe *(df)* e uma tabela temporária *(CountResults*) com algumas colunas que são úteis para a análise preditiva. `sqlContext`é utilizado para realizar transformações em dados estruturados. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    As quatro colunas de interesse no quadro de dados são **id,** **nome,** **resultados**e **violações.**

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

Vamos começar a perceber o que o conjunto de dados contém. 

1. Executar o seguinte código para mostrar os valores distintos na coluna de **resultados:**

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

    A `%%sql` magia `-o countResultsdf` seguida garante que a saída da consulta é permanecida localmente no servidor Jupyter (tipicamente o headnode do cluster). A saída é persistida como um quadro de dados [pandas](https://pandas.pydata.org/) com a contagem de nomes **especificadaResultsdf**. Para obter mais `%%sql` informações sobre a magia, e outras magias disponíveis com o kernel PySpark, consulte [kernels disponíveis em cadernos Jupyter com clusters Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    O resultado é:

    ![Saída de consulta SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Saída de consulta SQL")


3. Também pode usar [o Matplotlib,](https://en.wikipedia.org/wiki/Matplotlib)uma biblioteca usada para construir visualização de dados, para criar um enredo. Como o enredo deve ser criado a partir da contagem localpersistenteResultsdf dataframe, o código de corte deve começar com a **countResultsdf** `%%local` magia. Isto garante que o código é executado localmente no servidor Jupyter.

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

    O resultado é:

    ![Produção de aplicação de aprendizagem automática de faíscas - gráfico de tartes com cinco resultados de inspeção distintos](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Saída de resultados de aprendizagem automática de faíscas")

    Para prever um resultado de inspeção alimentar, precisa desenvolver um modelo baseado nas violações. Como a regressão logística é um método de classificação binária, faz sentido agrupar os dados dos resultados em duas categorias: **Fail** and **Pass:**

   - Passe
       - Passe
       - Passe c/ condições
   - Reprovado
       - Reprovado
   - Eliminar
       - Negócios não localizados
       - Fora do Negócio

     Os dados com os outros resultados ("Business Not Located" ou "out of Business") não são úteis, e constituem uma percentagem muito pequena dos resultados de qualquer forma.

4. Executar o seguinte código para converter`df`o quadro de dados existente, num novo quadro de dados onde cada inspeção é representada como um par de violações de etiquetas. Neste caso, um `0.0` rótulo de representa uma `1.0` falha, um rótulo de `-1.0` representa um sucesso, e um rótulo de representa alguns resultados para além destes dois. 

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

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística a partir do quadro de dados de entrada

A tarefa final é converter os dados rotulados num formato que possa ser analisado por regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares vetoriais de características de etiqueta,* onde o "vetor de recurso" é um vetor de números que representam o ponto de entrada. Por isso, é necessário converter a coluna "violações", que é semi-estruturada e contém muitos comentários em texto livre, para uma série de números reais que uma máquina poderia facilmente compreender.

Uma abordagem padrão de aprendizagem automática para o processamento da linguagem natural é atribuir a cada palavra distinta um "índice", e depois passar um vetor para o algoritmo de aprendizagem automática de tal forma que o valor de cada índice contém a frequência relativa dessa palavra na cadeia de texto.

A MLlib fornece uma maneira fácil de realizar esta operação. Primeiro, "tokenize" cada violação cordas para obter as palavras individuais em cada corda. Em seguida, `HashingTF` use um para converter cada conjunto de fichas em um vetor de características que pode então ser passado para o algoritmo de regressão logística para construir um modelo. Conduz estoque todos estes passos com recurso a um "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Avaliar o modelo utilizando outro conjunto de dados

Pode utilizar o modelo que criou anteriormente para *prever* quais serão os resultados das novas inspeções, com base nas violações que foram observadas. Treinou este modelo no conjunto de dados **Food_Inspections1.csv**. Pode utilizar um segundo conjunto de dados, **Food_Inspections2.csv,** para *avaliar* a força deste modelo nos novos dados. Este segundo conjunto de dados **(Food_Inspections2.csv) encontra-se**no recipiente de armazenamento predefinido associado ao cluster.

1. Executar o seguinte código para criar um novo dataframe, **previsõesDf** que contenham a previsão gerada pelo modelo. O corte também cria uma tabela temporária chamada **Previsões** com base no quadro de dados.

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

1. Olha para uma das previsões. Executa este corte:

    ```PySpark
    predictionsDf.take(1)
    ```

   Há uma previsão para a primeira entrada no conjunto de dados de teste.
1. O `model.transform()` método aplica a mesma transformação a quaisquer novos dados com o mesmo esquema, e chega a uma previsão de como classificar os dados. Pode fazer algumas estatísticas simples para ter uma noção de quão precisas eram as previsões:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A saída parece ser a seguinte:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    O uso da regressão logística com a Spark dá-lhe um modelo preciso da relação entre descrições de violações em inglês e se um determinado negócio passaria ou falharia uma inspeção alimentar.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão
Agora pode construir uma visualização final para ajudá-lo a raciocinar sobre os resultados deste teste.

1. Começa-se por extrair as diferentes previsões e resultados da tabela temporária **previsões** criada anteriormente. As seguintes consultas separam a saída como *true_positive,* *false_positive,* *true_negative*e *false_negative.* Nas consultas abaixo, desliga a `-q` visualização utilizando e também `-o`economiza a saída (utilizando) como quadros de dados que podem ser usados com a `%%local` magia.

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

1. Por fim, utilize o seguinte corte para gerar o enredo usando **Matplotlib**.

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

    ![Produção de aplicações de aprendizagem automática de faíscas - percentagens de gráficos de tartes de inspeções alimentares falhadas.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Saída de resultados de aprendizagem automática de faíscas")

    Neste gráfico, um resultado "positivo" refere-se à inspeção alimentar falhada, enquanto um resultado negativo refere-se a uma inspeção aprovada.

## <a name="shut-down-the-notebook"></a>Desligue o caderno
Depois de ter terminado a execução da aplicação, deve encerrar o caderno para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Isto fecha e fecha o caderno.

## <a name="see-also"></a><a name="seealso"></a>Consulte também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
