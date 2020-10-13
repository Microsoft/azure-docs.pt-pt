---
title: Operacionalizar modelos de aprendizagem automática construídos por faíscas - Processo de Ciência de Dados de Equipa
description: Como carregar e marcar modelos de aprendizagem armazenados no Azure Blob Storage (WASB) com Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bb38a76de41885b6f39a1c6dce7c44bcb52a4d60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027448"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operacionalize modelos de aprendizagem automática construídos por faíscas

Este tópico mostra como operacionalizar um modelo de aprendizagem automática salvo (ML) usando python em clusters HDInsight Spark. Descreve como carregar modelos de aprendizagem automática que foram construídos usando o Spark MLlib e armazenados em Azure Blob Storage (WASB), e como os pontuar com conjuntos de dados que também foram armazenados em WASB. Mostra como pré-processar os dados de entrada, transformar funcionalidades utilizando as funções de indexação e codificação no conjunto de ferramentas MLlib e como criar um objeto de dados de ponto marcado que pode ser usado como entrada para pontuar com os modelos ML. Os modelos utilizados para pontuar incluem Regressão Linear, Regressão Logística, Modelos Florestais Aleatórios e Modelos de Árvores Impulsionadoras de Gradient.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Aglomerados de faíscas e cadernos Jupyter
Os passos de configuração e o código para operacionalizar um modelo ML são fornecidos neste walkthrough para a utilização de um cluster HDInsight Spark 1.6, bem como um cluster Spark 2.0. O código para estes procedimentos também está previsto nos cadernos jupyter.

### <a name="notebook-for-spark-16"></a>Caderno para Faísca 1.6
O [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook mostra como operacionalizar um modelo salvo usando Python em clusters HDInsight. 

### <a name="notebook-for-spark-20"></a>Caderno para Faísca 2.0
Para modificar o caderno Jupyter para o Spark 1.6 utilizar com um cluster HDInsight Spark 2.0, substitua o ficheiro de código Python por [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Este código mostra como consumir os modelos criados na Spark 2.0.


## <a name="prerequisites"></a>Pré-requisitos

1. Você precisa de uma conta Azure e um cluster HdInsight Spark 1.6 (ou Spark 2.0) para completar esta passagem. Consulte a [visão geral da Data Science utilizando a Spark on Azure HDInsight](spark-overview.md) para obter instruções sobre como satisfazer estes requisitos. Este tópico também contém uma descrição dos dados de táxi de NYC 2013 usados aqui e instruções sobre como executar código a partir de um caderno Jupyter no cluster Spark. 
2. Crie os modelos de machine learning a serem pontuados aqui, trabalhando através da [exploração de Dados e modelação com](spark-data-exploration-modeling.md) o tópico Spark para o cluster Spark 1.6 ou os cadernos Spark 2.0. 
3. Os cadernos Spark 2.0 utilizam um conjunto de dados adicionais para a tarefa de classificação, o conhecido conjunto de dados de partida on-time da Companhia Aérea de 2011 e 2012. Uma descrição dos cadernos e ligações a eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do mostrado aqui. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuração: locais de armazenamento, bibliotecas e o contexto de faísca predefinido
A faísca é capaz de ler e escrever para uma bolha de armazenamento Azure (WASB). Assim, qualquer um dos seus dados existentes armazenados lá pode ser processado usando Spark e os resultados armazenados novamente em WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificado corretamente. O recipiente predefinido ligado ao cluster Spark pode ser referenciado utilizando um caminho que começa por: *"wasb//"*. A amostra de código a seguir especifica a localização dos dados a ler e o caminho para o diretório de armazenamento do modelo para o qual a saída do modelo é guardada. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para locais de armazenamento em WASB
Os modelos são guardados em: "wasb:///user/remoteuser/NYCTaxi/Models". Se este caminho não estiver bem definido, os modelos não estão carregados para pontuar.

Os resultados marcados foram guardados em: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Se o caminho para a pasta estiver incorreto, os resultados não são guardados nessa pasta.   

> [!NOTE]
> As localizações do caminho do ficheiro podem ser copiadas e coladas nos espaços reservados deste código a partir da saída da última célula do caderno **machine-learning-data-science-spark-data-exploration-modeling.ipynb.**   
> 
> 

Aqui está o código para definir caminhos de diretório: 

```python
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
```

**SAÍDA:**

datatime.datetime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importar bibliotecas
Definir contexto de faísca e importar bibliotecas necessárias com o seguinte código

```python
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
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de faísca predefinido e magias PySpark
Os núcleos PySpark que são fornecidos com cadernos Jupyter têm um contexto predefinido. Por isso, não precisa de definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com a aplicação que está a desenvolver. Estes contextos estão disponíveis por padrão:

* sc - para Spark 
* sqlContext - para Hive

O kernel PySpark fornece algumas "magias" predefinidas, que são comandos especiais que se pode chamar com %%. Há dois comandos que são usados nestas amostras de código.

* **%%local** Especificou que o código nas linhas subsequentes é executado localmente. O código deve ser o código Python válido.
* **%%sql -o \<variable name>** 
* Executa uma consulta de Colmeia contra o sqlContext. Se o parâmetro -o for aprovado, o resultado da consulta é persistido no contexto python %%local como um dataframe pandas.

Para obter mais informações sobre os núcleos para os cadernos Jupyter e as "magias" predefinidas que eles fornecem, consulte [Kernels disponível para cadernos Jupyter com clusters HDInsight Spark Linux em HDInsight.](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingerir dados e criar um quadro de dados limpo
Esta secção contém o código para uma série de tarefas necessárias para ingerir os dados a serem pontuados. Leia numa amostra de 0,1% da viagem de táxi e do ficheiro de tarifas (armazenado como ficheiro .tsv), formate os dados e, em seguida, cria um quadro de dados limpo.

Os ficheiros de viagem de táxi e tarifas foram unidos com base no procedimento fornecido no: [O Processo de Ciência de Dados de Equipa em ação: usando o tópico de clusters HDInsight Hadoop.](hive-walkthrough.md)

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 46,37 segundos

## <a name="prepare-data-for-scoring-in-spark"></a>Preparar dados para pontuar em Spark
Esta secção mostra como indexar, codificar e escalar características categóricas para prepará-las para serem usadas em algoritmos de aprendizagem supervisionados pela MLlib para classificação e regressão.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformação de recursos: índice e codificação de funcionalidades categóricas para a entrada em modelos para pontuação
Esta secção mostra como indexar dados categóricos usando funcionalidades de a `StringIndexer` e codificar com `OneHotEncoder` a entrada nos modelos.

O [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica uma coluna de etiquetas de cordas a uma coluna de índices de etiquetas. Os índices são encomendados por frequências de etiquetagem. 

O [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapeia uma coluna de índices de etiquetas para uma coluna de vetores binários, com no máximo um único valor único. Esta codificação permite que algoritmos que esperam funcionalidades de valor contínuo, como a regressão logística, sejam aplicadas a características categóricas.

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 5,37 segundos

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Criar objetos RDD com matrizes de funcionalidades para entrada em modelos
Esta secção contém código que mostra como indexar dados de texto categóricos como um objeto RDD e codificar um só quente para que possa ser usado para treinar e testar modelos de regressão logística MLlib e modelos à base de árvores. Os dados indexados são armazenados em objetos [Resilient Distributed Dataset (RDD).](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) Os RDDs são a abstração básica em Spark. Um objeto RDD representa uma coleção imutável e dividida de elementos que podem ser operados em paralelo com a Faísca.

Também contém código que mostra como escalar dados com os `StandardScalar` fornecidos pela MLlib para uso em regressão linear com a Descida de Gradiente Stochastic (SGD), um algoritmo popular para treinar uma vasta gama de modelos de aprendizagem automática. O [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) é utilizado para escalar as características para a variação da unidade. O dimensionamento de recursos, também conhecido como normalização de dados, assegura que as características com valores amplamente desembolsados não recebem um peso excessivo na função objetiva. 

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 11,72 segundos

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Marque com o Modelo de Regressão Logística e poupe a saída para o blob
O código desta secção mostra como carregar um Modelo de Regressão Logística que foi guardado no armazenamento de bolhas Azure e usá-lo para prever se uma gorjeta é ou não paga numa viagem de táxi, marcá-la com métricas de classificação padrão e, em seguida, guardar e traçar os resultados para o armazenamento de bolhas. Os resultados pontuados são armazenados em objetos RDD. 

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 19,22 segundos

## <a name="score-a-linear-regression-model"></a>Marque um modelo linear de regressão
Usamos [a LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) para treinar um modelo linear de regressão usando a Descida de Gradiente Stochastic (SGD) para otimização para prever o valor da gorjeta paga. 

O código desta secção mostra como carregar um Modelo linear de regressão a partir do armazenamento do blob Azure, marcar usando variáveis em escala e, em seguida, guardar os resultados de volta para a bolha.

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 16,63 segundos

## <a name="score-classification-and-regression-random-forest-models"></a>Classificação de pontuação e regressão De Modelos Florestais Aleatórios
O código desta secção mostra como carregar a classificação e regressão de modelos florestais aleatórios guardados guardados no armazenamento de bolhas Azure, marcar o seu desempenho com medidas de classificação e regressão padrão e, em seguida, guardar os resultados de volta para o armazenamento de bolhas.

[Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são conjuntos de árvores de decisão.  Combinam muitas árvores de decisão para reduzir o risco de sobremontagem. As florestas aleatórias podem lidar com características categóricas, estender-se até à definição de classificação multiclasse, não requerem escala de recursos e são capazes de capturar não linearidades e interações de recurso. As florestas aleatórias são um dos modelos de aprendizagem automática mais bem sucedidos para classificação e regressão.

[Spark.mllib](https://spark.apache.org/mllib/) suporta florestas aleatórias para classificação binária e multiclasse e para regressão, usando características contínuas e categóricas. 

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 31,07 segundos

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Classificação de pontuação e regressão Gradient Boosting Models
O código desta secção mostra como carregar a classificação e regressão Gradient Models de Aumento de Árvores do armazenamento de blob Azure, marcar o seu desempenho com medidas de classificação e regressão padrão e, em seguida, guardar os resultados de volta para o armazenamento de bolhas. 

**Spark.mllib** suporta GBTS para classificação binária e para regressão, usando características contínuas e categóricas. 

[As árvores de aumento de gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) são conjuntos de árvores de decisão. GBTS treina as árvores de decisão iterativamente para minimizar uma função de perda. O GBTS consegue lidar com funcionalidades categóricas, não requer escalamento de recursos e é capaz de capturar não linearidades e interações de recurso. Este algoritmo também pode ser usado numa definição de classificação multiclasse.

```python
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
```

**SAÍDA:**

Tempo de execução acima da célula: 14,6 segundos

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Limpe objetos da memória e imprima localizações de ficheiros pontuados

```python
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
```

**SAÍDA:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

RandomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

RandomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

Reforço daTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

Reforço da Região de ÁrvoreFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Consumir Modelos de faísca através de uma interface web
A Spark fornece um mecanismo para submeter remotamente trabalhos de lote ou consultas interativas através de uma interface REST com um componente chamado Livy. A Livy está ativada por padrão no seu cluster HDInsight Spark. Para obter mais informações sobre a Livy, consulte: [Submeta os trabalhos de faísca remotamente utilizando a Livy.](../../hdinsight/spark/apache-spark-livy-rest-interface.md) 

Pode usar livy para submeter remotamente um trabalho que obtém um ficheiro que é armazenado numa bolha Azure e, em seguida, escreve os resultados para outra bolha. Para isso, você carrega o script Python de  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) para a bolha do aglomerado de faíscas. Pode utilizar uma ferramenta como **o Microsoft Azure Storage Explorer** ou a **AzCopy** para copiar o script para a bolha do cluster. No nosso caso, enviamos o guião para ***wasb:///example/python/ConsumeGBNYCReg.py.***   

> [!NOTE]
> As chaves de acesso de que necessita podem ser encontradas no portal para a conta de armazenamento associada ao cluster Spark. 
> 
> 

Uma vez enviado para este local, este script é executado dentro do cluster Spark num contexto distribuído. Carrega o modelo e executa previsões em ficheiros de entrada com base no modelo.  

Pode invocar este script remotamente fazendo um simples pedido HTTPS/REST na Livy.  Aqui está um comando de caracóis para construir o pedido HTTP para invocar remotamente o script Python. Substitua clusterlogin, CLUSTERPASSWORD, CLUSTERNAME pelos valores adequados para o seu cluster Spark.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

Pode utilizar qualquer idioma no sistema remoto para invocar o trabalho Spark através da Livy, fazendo uma simples chamada HTTPS com Autenticação Básica.   

> [!NOTE]
> Seria conveniente utilizar a biblioteca Python Requests ao escamar esta chamada HTTP, mas não está atualmente instalada por padrão em Funções Azure. Em vez disso, são utilizadas bibliotecas HTTP mais antigas.   
> 
> 

Aqui está o código Python para a chamada HTTP:

```python
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
```

Também pode adicionar este código Python às [Funções Azure](https://azure.microsoft.com/documentation/services/functions/) para desencadear uma submissão de emprego spark que marca uma bolha com base em vários eventos como um temporizador, criação ou atualização de uma bolha. 

Se preferir uma experiência de cliente livre de código, utilize as [Apps Azure Logic](https://azure.microsoft.com/documentation/services/app-service/logic/) para invocar a pontuação do lote Spark, definindo uma ação HTTP no **Logic Apps Designer** e definindo os seus parâmetros. 

* A partir do portal Azure, crie uma nova App Lógica selecionando **+New**  ->  **Web + Mobile**Logic  ->  **App**. 
* Para apresentar o **Logic Apps Designer,** insira o nome da Aplicação Lógica e plano de serviço de aplicações.
* Selecione uma ação HTTP e introduza os parâmetros indicados na seguinte figura:

![Estruturador de Aplicações Lógicas](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>O que se segue?
**Transparecimento e varredura de hiperparímetros**: Consulte [a exploração e modelação avançadas de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando a validação cruzada e a varredura de hiper-parâmetros.

