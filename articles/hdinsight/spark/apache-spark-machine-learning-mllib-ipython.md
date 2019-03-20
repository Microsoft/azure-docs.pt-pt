---
title: Exemplo de aprendizado de máquina com o Spark MLlib no HDInsight - Azure
description: Saiba como utilizar o Spark MLlib para criar uma aplicação de aprendizado de máquina que analisa um conjunto de dados através da classificação por meio de regressão logística.
keywords: aprendizagem do spark, o exemplo do machine learning do spark
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: hrasheed
ms.openlocfilehash: bf29fd8d9b707636fb5965669ad800517a6cf58f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075566"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Utilizar o Apache Spark MLlib para criar uma aplicação de machine learning e analisar um conjunto de dados

Saiba como utilizar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar uma aplicação de machine learning para fazer uma análise preditiva simple num conjunto de dados aberta. Da máquina incorporada do Spark bibliotecas de aprendizagem, este exemplo utiliza *classificação* por meio de regressão logística. 

MLlib é uma biblioteca de Spark core que fornece muitos utilitários útil para tarefas de machine learning, incluindo utilitários que são adequados para:

* Classificação
* Regressão
* Clustering
* Modelagem de tópico
* Decomposição de valor singulares (programa) e a análise de componente principal (PCA)
* Hipótese, teste e calcular estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e regressão logística
*Classificação*, uma popular de machine learning tarefa, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação para saber como atribuir "etiquetas" Inserir dados, que fornece. Por exemplo, poderia pensar num algoritmo de machine learning que aceita as informações das ações como entrada e divide o estoque em duas categorias: stocks que deve vender e ações que deve ter.

Regressão logística é o algoritmo que utiliza para a classificação. Regressão logística do Spark API é útil para *classificação binária*, ou classificar os dados de entrada num de dois grupos. Para obter mais informações sobre a logística regressões, consulte [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser utilizado para prever a probabilidade de que um vetor de entrada pertence a um grupo ou outro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de Análise Preditiva em dados de inspeção alimentar
Neste exemplo, utiliza o Spark para executar alguma Análise Preditiva em dados de inspeção de comida (**Food_Inspections1.csv**) que foi adquirida por meio do [portal de dados da cidade de Chicago](https://data.cityofchicago.org/). Este conjunto de dados contém informações sobre inspeções de estabelecimento de alimento que foram realizados em Chicago, incluindo informações sobre cada estabelecimento, as violações encontradas (se aplicável) e os resultados da inspeção. O ficheiro de dados do CSV já está disponível na conta de armazenamento associada ao cluster em **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Os passos abaixo, vai desenvolver um modelo para ver o que é necessário para aprovação ou reprovação uma inspeção de alimento.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Criar uma aplicação do Apache Spark MLlib machine learning

1. Crie um bloco de notas do Jupyter com o kernel de PySpark. Para obter as instruções, veja [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importe os tipos necessários para esta aplicação. Copie e cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Por causa do kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código. 

## <a name="construct-the-input-dataframe"></a>Construir o pacote de dados de entrada

Uma vez que os dados não processados estão num formato CSV, pode utilizar o contexto do Spark para extrair o ficheiro na memória como texto não estruturado e, em seguida, utilize a biblioteca CSV do Python para analisar cada linha dos dados.

1. Execute as seguintes linhas para criar um Resiliente distribuído conjunto de dados (RDD) ao importar e analisar os dados de entrada.

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

2. Execute o seguinte código para recuperar uma linha de RDD, portanto, pode dar uma olhada do esquema de dados:

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

    A saída dá uma idéia do esquema do ficheiro de entrada. Ele inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados das inspeções e a localização, entre outras coisas. 

3. Execute o seguinte código para criar um pacote de dados (*df*) e uma tabela temporária (*CountResults*) com algumas colunas que são úteis para a Análise Preditiva. `sqlContext` é utilizado para efetuar transformações em dados estruturados. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    As quatro colunas de interesse no pacote de dados são **id**, **nome**, **resultados**, e **violações**.

4. Execute o seguinte código para obter uma pequena amostra dos dados:

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

Vamos começar a perceber o que contém o conjunto de dados. 

1. Execute o seguinte código para mostrar os valores distintos na **resultados** coluna:

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

2. Execute o seguinte código para visualizar a distribuição desses resultados:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    O `%%sql` seguido de mágica `-o countResultsdf` garante que o resultado da consulta persistem localmente no servidor do Jupyter (normalmente, o nó principal do cluster). A saída é persistente como uma [Pandas](https://pandas.pydata.org/) dataframe com o nome especificado **countResultsdf**. Para obter mais informações sobre o `%%sql` mágica e sobre outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    O resultado é:

    ![Resultado da consulta SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "resultado da consulta SQL")


3. Também pode utilizar [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), uma biblioteca utilizada para criar a visualização de dados, para criar um desenho. Porque o desenho tem de ser criado de localmente persistente **countResultsdf** pacote de dados, o trecho de código tem de começar com o `%%local` mágica. Isto garante que o código é ser executado localmente no servidor do Jupyter.

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

    ![Resultado de Spark aplicação de machine learning - gráfico de pizza com cinco resultados de inspeções distintos](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "saída de resultado de aprendizagem de Spark")

    Para prever um resultado de inspeção de comida, terá de desenvolver um modelo com base nas violações. Como a regressão logística é um método de classificação binária, faz sentido para agrupar os dados de resultado em duas categorias: **Falhar** e **passar**:

   - Passagem
       - Passagem
       - Passar com condições
   - Falha
       - Falha
   - Dispensar
       - Negócios não localizado
       - Fora do negócio

     Dados com os resultados ("Business não localizado" ou "fora da empresa") não são úteis, e eles formam uma percentagem muito pequena dos resultados de qualquer forma.

4. Execute o seguinte código para converter o pacote de dados existente (`df`) para um dataframe do novo onde cada inspeção é representada como um par de violações de etiqueta. Neste caso, uma etiqueta de `0.0` representa uma falha, a etiqueta `1.0` representa o sucesso e uma etiqueta de `-1.0` representa alguns resultados além desses dois. 

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

5. Execute o seguinte código para mostrar uma linha dos dados etiquetadas:

    ```PySpark
    labeledData.take(1)
    ```

    O resultado é:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística do pacote de dados de entrada

A tarefa final é converter os dados com nome num formato que pode ser analisado por regressão logística. A entrada para um algoritmo de regressão logística tem de ser um conjunto de *pares de vetor de funcionalidade de etiquetas*, em que o vetor"recurso" é um vetor de números que representa o ponto de entrada. Por isso, precisa converter a coluna "violações", que é semiestruturados e contém muitos comentários em texto de gratuitos, como uma matriz de números reais que uma máquina foi possível compreender facilmente.

Uma abordagem para o processamento de linguagem natural de aprendizado de máquina padrão é atribuir cada palavra distinta "index" e, em seguida, passar um vetor para o algoritmo de machine learning, de modo a que o valor de cada índice contém a frequência relativa nessa palavra na cadeia de texto.

MLlib fornece uma forma fácil de executar esta operação. Em primeiro lugar, "indexar" cada cadeia de caracteres de violações para obter as palavras individuais em cada cadeia de caracteres. Em seguida, utilize um `HashingTF` para converter cada conjunto de tokens num vetor de funcionalidade que, em seguida, pode ser transmitido para o algoritmo de regressão logística para construir um modelo. Realizar todos estes passos em sequência através de "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Avalie o modelo utilizando outro conjunto de dados

Pode usar o modelo que criou anteriormente ao *prever* o que os resultados de inspeções novos serão, com base nas violações que foram observadas. Esse modelo no conjunto de dados de preparação **Food_Inspections1.csv**. Pode utilizar um segundo conjunto de dados, **Food_Inspections2.csv**ao *avaliar* a vantagem desse modelo sobre os novos dados. Este segundo conjunto de dados (**Food_Inspections2.csv**) está no contentor de armazenamento predefinida associado ao cluster.

1. Execute o seguinte código para criar um novo pacote de dados, **predictionsDf** que contém a predição gerada pelo modelo. O trecho de código também cria uma tabela temporária denominada **previsões** com base no pacote de dados.

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

1. Ver uma das predições. Execute este trecho de código:

    ```PySpark
    predictionsDf.take(1)
    ```

   Há uma previsão para a primeira entrada no conjunto de dados de teste.
1. O `model.transform()` método aplica-se a mesma transformação a quaisquer dados novo com o mesmo esquema e chegar a uma predição de como classificar os dados. Pode fazer algumas estatísticas simples para obter uma noção do grau de precisão de predições foram:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    O resultado é semelhante ao seguinte:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Utilizar regressão logística com o Spark dá-lhe um modelo preciso da relação entre as descrições de violações em inglês e se uma determinada empresa seria aprovação ou reprovação uma inspeção de alimento.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual de predição
Agora pode construir uma visualização final para o ajudar a ponderar os resultados desse teste.

1. Comece por extrair o previsões diferentes e os resultados do **previsões** tabela temporária que criou anteriormente. As seguintes consultas de separam a saída *true_positive*, *false_positive*, *true_negative*, e *false_negative*. Em consultas abaixo, desativar a visualização, utilizando `-q` e também salvar o resultado (utilizando `-o`) como pacotes que podem ser utilizadas com o `%%local` mágica.

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

1. Por fim, utilize o seguinte fragmento para gerar a através de plotagem **Matplotlib**.

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

    ![Spark aprendizagem saída da aplicação - porcentagens de gráfico circular de inspeções alimentares com falha. ](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Saída de resultado de aprendizagem de Spark")

    Neste gráfico, um resultado "positivo" refere-se para a inspeção de comida com falha, enquanto um resultado negativo refere-se para uma inspeção com êxito.

## <a name="shut-down-the-notebook"></a>Encerrar o bloco de notas
Depois de terminar de executar a aplicação, deve encerrar o bloco de notas para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Isso encerra e fecha o bloco de notas.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
