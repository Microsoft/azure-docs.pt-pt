---
title: Operacionalizar modelos de aprendizagem de máquina criados com o Spark - Team Data Science Process
description: Como carregar e classificar modelos de aprendizagem armazenados no armazenamento de Blobs do Azure (WASB) com o Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d89886e7cc5fe47013902b281c490b79a07e7641
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888115"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operacionalizar modelos de aprendizagem automática criados com o Spark

Este tópico mostra como operacionalizar um modelo de aprendizagem guardado (ML) com o Python nos clusters do Spark do HDInsight. Ele descreve como carregar modelos de machine learning que foram criados com o Spark MLlib e armazenados no armazenamento de Blobs do Azure (WASB) e como classificá-los com conjuntos de dados que também tiverem sido armazenados no WASB. Ele mostra como processar previamente os dados de entrada, transforme recursos usando as funções de codificação e indexação no Kit de ferramentas MLlib e como criar um objeto de dados do ponto de etiquetados que pode ser utilizado como entrada para a classificação com os modelos de ML. Os modelos usados para a classificação incluem regressão Linear, regressão logística, aleatório de floresta de modelos e modelos de árvore aumentam a gradação.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Os clusters do Spark e blocos de notas do Jupyter
Passos de configuração e o código de modo a operacionalizar um modelo de ML são fornecidos esta explicação passo a passo para utilizar um cluster do HDInsight Spark 1.6, bem como um cluster do Spark 2.0. O código para estes procedimentos também é fornecido em blocos de notas do Jupyter.

### <a name="notebook-for-spark-16"></a>Bloco de notas para o Spark 1.6
O [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) bloco de notas do Jupyter mostra como operacionalizar um modelo guardado com o Python nos clusters do HDInsight. 

### <a name="notebook-for-spark-20"></a>Bloco de notas para o Spark 2.0
Para modificar o bloco de notas do Jupyter para Spark 1.6 utilizar com um cluster do HDInsight Spark 2.0, substitua o ficheiro de código de Python com [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Este código mostra como consumir os modelos criados no Spark 2.0.


## <a name="prerequisites"></a>Pré-requisitos

1. Precisa de uma conta do Azure e um Spark 1.6 (ou Spark 2.0) cluster do HDInsight para concluir estas instruções. Consulte a [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md) para obter instruções sobre como atender a esses requisitos. Esse tópico também contém uma descrição dos dados de táxis de 2013 NYC usados aqui e obter instruções sobre como executar o código a partir de um bloco de notas do Jupyter no cluster do Spark. 
2. Também tem de criar os modelos de machine learning ser classificados aqui ao trabalhar com o [exploração de dados e modelação com o Spark](spark-data-exploration-modeling.md) tópico para o cluster do Spark 1.6 ou os blocos de notas do Spark 2.0. 
3. Os blocos de notas do Spark 2.0 utilizam um conjunto de dados adicional para a tarefa de classificação, bem conhecido companhia aérea pontuais mudança dataset a partir de 2011 e 2012. Uma descrição dos blocos de notas e links para eles são fornecidos na [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e o gerenciamento de cluster podem ser ligeiramente diferentes do que é mostrado aqui. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Programa de configuração: localizações de armazenamento, bibliotecas e o contexto de Spark predefinido
O Spark é capaz de ler e escrever para um Blob de armazenamento do Azure (WASB). Portanto, qualquer um dos seus dados existentes armazenados aqui podem ser processados com o Spark e os resultados novamente armazenados a ser WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificadas corretamente. O contentor predefinido ligado para o cluster do Spark pode ser referenciado com um início do caminho com: *"wasb / / /"*. O código de exemplo seguinte especifica a localização dos dados a ser lido e o caminho para o diretório de armazenamento de modelo ao qual o resultado de modelo é guardado. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para localizações de armazenamento no WASB
Modelos são salvas em: "wasb: / / / utilizador/utilizadorremoto/NYCTaxi/modelos". Se este caminho não for definido corretamente, os modelos não são carregados para a classificação.

Os resultados com a pontuação foram guardados no: "wasb: / / / utilizador/utilizadorremoto/NYCTaxi/ScoredResults". Se o caminho para a pasta estiver incorreto, os resultados não são guardados nessa pasta.   

> [!NOTE]
> As localizações de caminho de ficheiro podem ser copiadas e coladas os marcadores de posição nesse código a partir da saída da última célula do **machine-learning-data-science-spark-data-exploration-modeling.ipynb** bloco de notas.   
> 
> 

Aqui está o código para definir os caminhos de diretório: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**SAÍDA:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Bibliotecas de importação
Definir o contexto do spark e importe bibliotecas necessárias com o código a seguir

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de Spark predefinido e a magia do PySpark
PySpark kernels que são fornecidos com blocos de notas do Jupyter têm um contexto predefinido. Portanto, não é necessário definir o Spark ou do Hive contextos explicitamente antes de começar a trabalhar com a aplicação estiver a desenvolver. Estas estão disponíveis para por predefinição. Desses contextos são:

* SC - para o Spark 
* kontext sqlContext - para o Hive

O kernel do PySpark fornece alguns predefinidas "magia", que são comandos especiais que pode chamar com % %. Existem dois desses comandos que são utilizados nestas amostras de código.

* **% % local** especificado que o código nas linhas subsequentes é executado localmente. Código tem de ser um código de Python válido.
* **% % sql -o <variable name>** 
* Executa uma consulta do Hive contra o kontext sqlContext. Se o parâmetro -o passado, o resultado da consulta é mantido no % % contexto Python local como um Pandas dataframe.

Para obter mais informações sobre os kernels de blocos de notas do Jupyter e predefinidos "magics" que fornecem, consulte [Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux clusters no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingestão de dados e criar um quadro de dados de membros limpos
Esta secção contém o código de uma série de tarefas necessárias para ingestão de dados ser classificados. Ler um exemplo de 0,1% associado ao souboru táxis viagem e Europeia (armazenado como um ficheiro. tsv), formato os dados e, em seguida, cria um quadro de dados limpa.

Os ficheiros de viagem e Europeia táxis foram associados com base no procedimento fornecido no: [O processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md) tópico.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 46.37 segundos

## <a name="prepare-data-for-scoring-in-spark"></a>Preparar dados para a classificação no Spark
Esta secção mostra como indexar, codificar e dimensionar funcionalidades categóricas para prepará-los para utilização em algoritmos de aprendizagem MLlib supervisionado para classificação e regressão.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformação de funcionalidade: índice e codificar categóricas funcionalidades de entrada em modelos para a classificação
Esta secção mostra como indexar dados categóricos, com um `StringIndexer` e codificar funcionalidades com `OneHotEncoder` de entrada para os modelos.

O [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica uma coluna de cadeia de caracteres de etiquetas para uma coluna de índices de etiqueta. Os índices são ordenados por frequências de etiqueta. 

O [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) uma coluna de índices de etiqueta é mapeado para uma coluna de vetores de binários, no máximo um único um valor. Esta codificação permite que os algoritmos que esperam contínuos recursos importantes, como a regressão logística, possam ser aplicadas aos recursos categóricos.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 5.37 segundos

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Criar objetos RDD com matrizes de funcionalidade de entrada em modelos
Esta secção contém código que mostra como indexar dados categóricos texto como um objeto RDD e um-hot codificá-lo para que possa ser utilizada para dar formação e teste de regressão logística de MLlib e modelos baseados em árvore. Os dados indexados são armazenados no [conjunto de dados distribuídos resilientes (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objetos. Estes são a abstração básica no Spark. Um objeto RDD representa uma coleção particionada imutável de elementos que pode ser manipulado em paralelo com o Spark.

Também contém o código que mostra como dimensionar os dados com o `StandardScalar` fornecida pelo MLlib para utilização em regressão linear com stochastic Gradient gradiente descendente (SGD), um algoritmo popular para uma vasta gama de modelos de aprendizagem automática de formação. O [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) é usado para dimensionar os recursos a variância de unidade. Dimensionamento de recursos, também conhecido como normalização de dados, forma, assegura que funcionalidades com valores amplamente disbursed são não fornecido excessiva pesar na função Objetiva. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 11.72 segundos

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Com o modelo de regressão logística de pontuação e guarde a saída para o blob
O código nesta secção mostra como carregar um modelo de regressão logística, que foi guardado no armazenamento de Blobs do Azure e utilizá-lo para prever se ou não uma dica é pago numa viagem de táxis, pontuação-lo com a métrica de classificação padrão e, em seguida, guarde e desenhar os resultados para o blob stora ge. Os resultados com a pontuação são armazenados em objetos RDD. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:**

Tempo decorrido para executar acima célula: 19.22 segundos

## <a name="score-a-linear-regression-model"></a>Um modelo de regressão Linear de pontuação
Usamos [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) para preparar um modelo de regressão linear usando stochastic Gradient gradiente descendente (SGD) para a otimização para prever a quantidade de sugestão pago. 

O código nesta secção mostra como carregar um modelo de regressão Linear do armazenamento de Blobs do Azure, utilizando variáveis de dimensionado de pontuação e, em seguida, guardar os resultados novamente para o blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA:**

Tempo decorrido para executar acima célula: 16.63 segundos

## <a name="score-classification-and-regression-random-forest-models"></a>Classificar modelos de floresta de aleatório classificação e regressão
O código nesta secção mostra como carregar a classificação guardada e modelos de floresta aleatório de regressão guardados no armazenamento de Blobs do Azure, classificar o desempenho com classificador padrão e medidas de regressão e, em seguida, guardar os resultados novamente para o armazenamento de Blobs.

[Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são árvores de árvores de decisões.  Elas combinam muitos árvores de decisão para reduzir o risco de overfitting. Florestas aleatórias podem lidar com recursos categóricos, expandir para a definição de classificação multiclasses, não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Florestas aleatórias são uma do mais bem-sucedidas modelos de machine learning para classificação e regressão.

[spark.mllib](https://spark.apache.org/mllib/) suporta florestas aleatórias de classificação binária e várias classes e para regressão, usando recursos contínuos e categóricos. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:**

Tempo decorrido para executar acima célula: 31.07 segundos

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Classificação e regressão gradação adaptativo árvore modelos de pontuação
O código nesta secção mostra como carregar classificação e regressão gradação adaptativo árvore modelos do armazenamento de Blobs do Azure, classificar o desempenho com classificador padrão e medidas de regressão e, em seguida, guardar os resultados novamente para o armazenamento de Blobs. 

**spark.mllib** suporta GBTs para classificação binária e regressão, usando recursos contínuos e categóricos. 

[Árvores de aumentam a gradação](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são árvores de árvores de decisões. GBTs treinar árvores de decisão iterativamente para minimizar a uma função de perda. GBTs pode lidar com recursos categóricos, não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Eles também podem ser usados numa configuração de classificação de várias classes.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 14.6 segundos

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Limpar objetos da memória e localizações de ficheiros com a pontuação de impressão
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**SAÍDA:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Consumir modelos de Spark através de uma interface da web
Spark fornece um mecanismo para submeter remotamente tarefas de lote ou consultas interativas através de uma interface REST com um componente chamado Livy. Livy está ativada por predefinição no seu cluster do HDInsight Spark. Para obter mais informações sobre o Livy, consulte: [Submeter tarefas do Spark remotamente com o Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Pode utilizar Livy remotamente submeter uma tarefa que o batch pontuações de um ficheiro que é armazenado num blob do Azure e, em seguida, grava os resultados em outro blob. Para fazer isso, carrega o script de Python de  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) para o blob do cluster do Spark. Pode usar uma ferramenta como o **Explorador de armazenamento do Microsoft Azure** ou **AzCopy** para copiar o script para o blob de cluster. No nosso caso, nós carregamos o script para ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> As chaves de acesso que precisa pode ser encontrada no portal para a conta de armazenamento associada ao cluster do Spark. 
> 
> 

Depois de carregado nesta localização, este script é executado dentro do cluster do Spark num contexto distribuído. Ele carrega o modelo e é executado previsões em ficheiros de entrada com base no modelo de.  

É possível invocar esse script remotamente, fazendo uma solicitação HTTPS/REST simples no Livy.  Eis um comando curl para construir o pedido HTTP para invocar o script de Python remotamente. Substitua os valores adequados para o seu cluster do Spark CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Pode usar qualquer linguagem no sistema remoto para invocar a tarefa do Spark através do Livy fazendo uma simple chamada HTTPS com a autenticação básica.   

> [!NOTE]
> Seria conveniente usar a biblioteca de Python pedidos ao fazer esta chamada HTTP, mas não está atualmente instalado por predefinição nas funções do Azure. Para que as bibliotecas mais antigas do HTTP são utilizadas em vez disso.   
> 
> 

Aqui está o código de Python para a chamada HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Também pode adicionar esse código de Python para [as funções do Azure](https://azure.microsoft.com/documentation/services/functions/) para acionar uma submissão da tarefa Spark pontua um blob com base em vários eventos como um temporizador, criação ou atualização de um blob. 

Se preferir uma experiência de cliente gratuito de código, utilize o [do Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) para invocar o lote de Spark de classificação, definindo uma ação de HTTP na **estruturador de aplicações lógicas** e a definição de seus parâmetros. 

* A partir do portal do Azure, crie uma nova aplicação lógica, selecionando **+ novo** -> **Web + móvel** -> **aplicação lógica**. 
* Para abrir o **estruturador de aplicações lógicas**, introduza o nome da aplicação lógica e o plano do serviço de aplicações.
* Selecione uma ação de HTTP e introduza os parâmetros mostrados na figura a seguir:

![Estruturador de Aplicações Lógicas](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Passos seguintes?
**A validação cruzada e varrimento de hiper-parâmetros**: Ver [avançada de exploração de dados e modelagem com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser treinado varrimento de validação cruzada e de hyper-parâmetro a utilizar.

