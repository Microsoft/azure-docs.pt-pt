---
title: Exploração e modelação de dados com Spark - Processo de Ciência de Dados de Equipa
description: Apresenta as capacidades de exploração e modelação de dados do kit de ferramentas Spark MLlib em HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 1ef6c678110037a4aed24ef40631b73a04ca6a9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028092"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com o Spark

Aprenda a usar o HDInsight Spark para treinar modelos de aprendizagem automática para previsão de tarifas de táxi usando Spark MLlib.

Esta amostra mostra os vários passos no [Processo de Ciência de Dados](./index.yml)de Equipa. Um subconjunto da viagem de táxi de NYC e dataset de tarifa 2013 é usado para carregar, explorar e preparar dados. Em seguida, usando spark MLlib, os modelos binários de classificação e regressão são treinados para prever se uma gorjeta será paga pela viagem e estimar o valor da gorjeta.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma conta Azure e um cluster HdInsight Spark 1.6 (ou Spark 2.0) para completar esta passagem. Consulte a [visão geral da Data Science utilizando a Spark on Azure HDInsight](spark-overview.md) para obter instruções sobre como satisfazer estes requisitos. Este tópico também contém uma descrição dos dados de táxi de NYC 2013 usados aqui e instruções sobre como executar código a partir de um caderno Jupyter no cluster Spark. 

### <a name="spark-clusters-and-notebooks"></a>Aglomerados de faíscas e cadernos

Os passos de configuração e o código são fornecidos neste walkthrough para a utilização de um HDInsight Spark 1.6. Mas os cadernos Jupyter são fornecidos tanto para os clusters HDInsight Spark 1.6 como para o Spark 2.0. Uma descrição dos cadernos e ligações a eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do mostrado aqui. Por conveniência, aqui estão os links para os cadernos Jupyter para Spark 1.6 (a ser executado no núcleo pySpark do servidor Jupyter Notebook) e Spark 2.0 (a ser executado no núcleo pySpark3 do servidor Jupyter Notebook):

- [Spark 1.6 cadernos](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): Forneça informações sobre como realizar a exploração de dados, modelação e pontuação com vários algoritmos diferentes.
- [Spark 2.0 Cadernos](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): Forneça informações sobre como executar tarefas de regressão e classificação. Os conjuntos de dados podem variar, mas os passos e conceitos são aplicáveis a vários conjuntos de dados.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> As descrições abaixo estão relacionadas com a utilização da Faísca 1.6. Para versões Spark 2.0, utilize os cadernos descritos e ligados acima. 

## <a name="setup"></a>Configuração

A faísca é capaz de ler e escrever para a Azure Storage Blob (também conhecida como WASB). Assim, qualquer um dos seus dados existentes armazenados lá pode ser processado usando Spark e os resultados armazenados novamente em WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificado corretamente. O recipiente predefinido ligado ao cluster Spark pode ser referenciado utilizando um caminho que começa por: "wasb:///". Outros locais são referenciados por "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para locais de armazenamento em WASB

A amostra de código a seguir especifica a localização dos dados a ler e o caminho para o diretório de armazenamento do modelo para o qual a saída do modelo é guardada:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Importar bibliotecas

A instalação requer também a importação de bibliotecas necessárias. Definir o contexto de faísca e importar bibliotecas necessárias com o seguinte código:

```python
# IMPORT LIBRARIES
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

Os núcleos PySpark que são fornecidos com cadernos Jupyter têm um contexto predefinido. Por isso, não precisa de definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com a aplicação que está a desenvolver. Estes contextos estão disponíveis para si por padrão. Estes contextos são:

* sc - para Spark 
* sqlContext - para Hive

O kernel PySpark fornece algumas "magias" predefinidas, que são comandos especiais que se pode chamar com %%. Há dois comandos que são usados nestas amostras de código.

* **%%local** Especifica que o código nas linhas subsequentes deve ser executado localmente. O código deve ser o código Python válido.
* **%%sql -o \<variable name>** Executa uma consulta de Colmeia contra o sqlContext. Se o parâmetro -o for aprovado, o resultado da consulta é persistido no contexto python %%local como um DataFrame pandas.

Para obter mais informações sobre os núcleos de cadernos Jupyter e as "magias" predefinidas, consulte [Kernels disponível para cadernos Jupyter com clusters HDInsight Spark Linux em HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Carregar os dados

O primeiro passo no processo de ciência de dados é ingerir os dados a serem analisados a partir de fontes onde reside no seu ambiente de exploração e modelação de dados. O ambiente é Spark nesta passagem. Esta secção contém o código para completar uma série de tarefas:

* ingerir a amostra de dados a ser modelada
* ler no conjunto de dados de entrada (armazenado como um ficheiro .tsv)
* formato e limpar os dados
* criar e cache objetos (RDDs ou quadros de dados) na memória
* registe-o como uma tabela temporária no contexto SQL.

Aqui está o código para a ingestão de dados.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**SAÍDA:**

Tempo de execução acima da célula: 51,72 segundos

## <a name="explore-the-data"></a>Explorar os dados

Uma vez que os dados tenham sido trazidos para a Spark, o próximo passo no processo de ciência de dados é obter uma compreensão mais profunda dos dados através da exploração e visualização. Nesta secção, examinamos os dados do táxi usando consultas SQL e traçamos as variáveis-alvo e as funcionalidades prospetivas para inspeção visual. Especificamente, traçamos a frequência das contagens de passageiros em viagens de táxi, a frequência dos valores das gorjetas, e como as gorjetas variam por valor de pagamento e tipo.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Traçar um histograma de frequências de contagem de passageiros na amostra de viagens de táxi

Este código e os subsequentes snippets usam magia SQL para consultar a amostra e a magia local para traçar os dados.

* **MAGIA SQL ( `%%sql` )** O kernel HDInsight PySpark suporta consultas de HiveQL fáceis em linha contra o sqlContext. O argumento (-o VARIABLE_NAME) persiste na saída da consulta SQL como um DataFrame pandas no servidor Jupyter. Esta definição disponibiliza a saída no modo local.
* A **`%%local` magia** é usada para executar código localmente no servidor Jupyter, que é o headnode do cluster HDInsight. Normalmente, usa-se `%%local` magia em conjunto com a magia com o parâmetro `%%sql` -o. O parâmetro -o persistiria a saída da consulta SQL localmente e, em seguida, a magia local %%desencadearia o próximo conjunto de código snippet para correr localmente contra a saída das consultas SQL que são persistidos localmente

A saída é visualizada automaticamente depois de executar o código.

Esta consulta recupera as viagens por contagem de passageiros. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Este código cria um quadro de dados local a partir da saída de consulta e traça os dados. A `%%local` magia cria um quadro de dados local, que pode ser usado para conspirar com `sqlResults` matplotlib. 

> [!NOTE]
> Esta magia PySpark é usada várias vezes nesta passagem. Se a quantidade de dados for grande, deve recolher amostras para criar um quadro de dados que possa caber na memória local.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Aqui está o código para traçar as viagens por contagens de passageiros

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**SAÍDA:**

![Frequência de viagem por contagem de passageiros](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pode selecionar entre vários tipos de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões de menu **Tipo** no caderno. O enredo do Bar é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Defina um histograma de montantes de gorjetas e como o valor da gorjeta varia conso por conta de passageiros e quantidades de tarifas.

Utilize uma consulta SQL para recolher dados de amostragem.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

Esta célula de código usa a consulta SQL para criar três parcelas dos dados.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**SAÍDA:** 

![Distribuição de montante de gorjeta](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Valor da gorjeta por contagem de passageiros](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Valor da gorjeta por tarifa](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Preparar os dados

Esta secção descreve e fornece o código para os procedimentos utilizados para preparar dados para utilização na modelação ML. Mostra como fazer as seguintes tarefas:

* Crie uma nova funcionalidade ao ligar horas aos baldes de tempo de tráfego
* Índice e codificação de características categóricas
* Criar objetos de ponto marcados para entrada em funções ML
* Criar uma subesmalte aleatória dos dados e dividi-lo em conjuntos de treino e teste
* Dimensionamento de funcionalidades
* Objetos de cache na memória

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Crie uma nova funcionalidade ao ligar horas aos baldes de tempo de tráfego

Este código mostra como criar uma nova funcionalidade, ligando horas em baldes de tempo de tráfego e, em seguida, como cache o quadro de dados resultante na memória. Onde os conjuntos de dados distribuídos resilientes (RDDs) e os quadros de dados são usados repetidamente, o caching leva a tempos de execução melhorados. Assim, cache RDDs e data-frames em várias fases da passagem. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**SAÍDA:**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexar e codificar características categóricas para a entrada em funções de modelação

Esta secção mostra como indexar ou codificar funcionalidades categóricas para a entrada nas funções de modelação. As funções de modelação e previsão do MLlib requerem funcionalidades com dados de entrada categóricos para serem indexadas ou codificadas antes da utilização. Dependendo do modelo, é necessário indexá-los ou codificá-los de diferentes formas:  

* **A modelação baseada em árvores** requer que as categorias sejam codificadas como valores numéricos (por exemplo, uma característica com três categorias pode ser codificada com 0, 1, 2). Este algoritmo é fornecido pela função [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) da MLlib. Esta função codifica uma coluna de etiquetas de cordas para uma coluna de índices de etiquetas que são encomendados por frequências de etiqueta. Embora indexados com valores numéricos para a entrada e tratamento de dados, os algoritmos baseados em árvores podem ser especificados para os tratar adequadamente como categorias. 
* **Os modelos logísticos e lineares de regressão** requerem codificação de um só hot, onde, por exemplo, uma característica com três categorias pode ser expandida em três colunas de recurso, com cada uma contendo 0 ou 1 dependendo da categoria de observação. A MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para fazer uma codificação única. Este codificadora mapeia uma coluna de índices de etiquetas para uma coluna de vetores binários, com no máximo um único valor único. Esta codificação permite que algoritmos que esperem características numéricas valorizadas, como a regressão logística, sejam aplicadas a características categóricas.

Aqui está o código para indexar e codificar características categóricas:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Tempo de execução acima da célula: 1,28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos de ponto marcados para entrada em funções ML

Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto rotulado e codificá-lo para que possa ser usado para treinar e testar a regressão logística MLlib e outros modelos de classificação. Os objetos de pontos rotulados são conjuntos de dados distribuídos resilientes (RDD) formatados de uma forma que é necessária como dados de entrada pela maioria dos algoritmos ML em MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou escasso, associado a um rótulo/resposta.  

Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados [de ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) e codificá-lo para que possa ser usado para treinar e testar a regressão logística MLlib e outros modelos de classificação. Os objetos de pontos rotulados são conjuntos de dados distribuídos resilientes (RDD) que consistem numa etiqueta (variável alvo/resposta) e vetor de características. Este formato é necessário como entrada por muitos algoritmos ML em MLlib.

Aqui está o código para indexar e codificar as características de texto para a classificação binária.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Aqui está o código para codificar e indexar funcionalidades de texto categórico para análise linear de regressão.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Criar uma subesmalte aleatória dos dados e dividi-lo em conjuntos de treino e teste

Este código cria uma amostragem aleatória dos dados (25% é usado aqui). Embora não seja necessário para este exemplo devido ao tamanho do conjunto de dados, demonstramos como pode provar aqui para que saiba como usá-lo para o seu próprio problema quando necessário. Quando as amostras são grandes, a amostragem pode economizar tempo significativo enquanto treina os modelos. Em seguida, dividimos a amostra numa parte de formação (75% aqui) e numa parte de teste (25% aqui) para usar na classificação e na modelação de regressão.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Tempo de execução acima da célula: 0,24 segundos

### <a name="feature-scaling"></a>Dimensionamento de funcionalidades

O dimensionamento de recursos, também conhecido como normalização de dados, assegura que as características com valores amplamente desembolsados não recebem um peso excessivo na função objetiva. O código de escala de funcionalidades utiliza o [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para escalar as funcionalidades para a variação da unidade. É fornecido pela MLlib para uso em regressão linear com Descida de Gradiente Stochastic (SGD), um algoritmo popular para a formação de uma vasta gama de outros modelos de aprendizagem automática, tais como regressões regularizadas ou máquinas de vetores de suporte (SVM).

> [!NOTE]
> Descobrimos que o algoritmo LinearRegressionWithSGD é sensível ao dimensionamento de recursos.

Aqui está o código para escalar variáveis para uso com o algoritmo linear regularizado SGD.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Tempo de execução acima da célula: 13,17 segundos

### <a name="cache-objects-in-memory"></a>Objetos de cache na memória

O tempo de treino e teste de algoritmos ML pode ser reduzido através da caching dos objetos de quadro de dados de entrada utilizados para classificação, regressão e características em escala.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:** 

Tempo de execução acima da célula: 0,15 segundos

## <a name="train-a-binary-classification-model"></a>Treine um modelo de classificação binário

Esta secção mostra como usar três modelos para a tarefa de classificação binária de prever se uma gorjeta é ou não paga para uma viagem de táxi. Os modelos apresentados são:

* Regressão logística regular 
* Modelo de floresta aleatória
* Árvores de aumento de gradiente

Cada secção de código de construção de modelos é dividida em passos: 

1. **Dados de formação de modelo** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em blob para consumo futuro

### <a name="classification-using-logistic-regression"></a>Classificação usando regressão logística

O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [o LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é ou não paga por uma viagem no conjunto de dados de táxi e tarifas de NYC.

**Treine o modelo de regressão logística usando cv e varredura de hiperparímetro**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:** 

Coeficientes: [0.0082065285375, -0.022365576104, -0.0183812028036, -3.48124578069e-05, -0.0024764694723, -0.00165897881503, 0.06753948373228, -0.1182313101, -0.32460912762, -0,204549780032, -1.36499216354, 0.59108507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.3.00439726852, -3.3.3.0043972685, -3.3.0043972685, -3.3.3.3.00439726852, 3.46567827545, -3.3.3.004397268 51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0,054518719222]

Interceção: -0.0111216486893

Tempo de execução acima da célula: 14,43 segundos

**Avaliar o modelo de classificação binária com métricas padrão**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**SAÍDA:** 

Área em RP = 0.985297691373

Área sob ROC = 0.983714670256

Estatísticas sumárias

Precisão = 0.984304060189

Recordação = 0.984304060189

F1 Pontuação = 0.984304060189

Tempo de execução acima da célula: 57,61 segundos

**Traçar a curva ROC.**

A *previsão ElabelsDF* está registada como uma tabela, *tmp_results,* na célula anterior. *tmp_results* pode ser usado para fazer consultas e resultados de saída no sqlResults data-frame para a trama. Aqui está o código.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Aqui está o código para fazer previsões e traçar a curva ROC.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**SAÍDA:**

![Regressão logística ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação florestal aleatória

O código nesta secção mostra como treinar, avaliar e salvar um modelo florestal aleatório que prevê se uma gorjeta é ou não paga por uma viagem na viagem de táxi de NYC e conjunto de dados de tarifas.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Área sob ROC = 0.985297691373

Tempo de execução acima da célula: 31,09 segundos

### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores de aumento de gradiente

O código nesta secção mostra como treinar, avaliar e salvar um modelo de árvores impulsionadoras de gradiente que prevê se uma gorjeta é ou não paga para uma viagem na viagem de táxi de NYC e conjunto de dados de tarifas.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Área sob ROC = 0.985297691373

Tempo de execução acima da célula: 19,76 segundos

## <a name="train-a-regression-model"></a>Preparar um modelo de regressão

Esta secção mostra como usar três modelos para a tarefa de regressão de prever o valor da gorjeta paga por uma viagem de táxi com base em outras características da ponta. Os modelos apresentados são:

* Regressão linear regularizada
* Floresta aleatória
* Árvores de aumento de gradiente

Estes modelos foram descritos na introdução. Cada secção de código de construção de modelos é dividida em passos: 

1. **Dados de formação de modelo** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em blob para consumo futuro

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD

O código nesta secção mostra como usar funcionalidades em escala para treinar uma regressão linear que utiliza a descida estocástica de gradiente (SGD) para otimização, e como marcar, avaliar e salvar o modelo no Azure Blob Storage (WASB).

> [!TIP]
> Na nossa experiência, pode haver problemas com a convergência dos modelos LinearRegressionWithSGD, e os parâmetros precisam de ser alterados/otimizados cuidadosamente para obter um modelo válido. A escala de variáveis ajuda significativamente na convergência.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

Coeficientes: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.0063741034526179, -0.0063719, -00637410344522, 0.545083566179, -0.00637410344522, 0.545083566179, -0.006379, -006379, -00637410344522, 0.545083566179, -0.0063179, -0063179, -00630.536756072402, 0.0105762393099, -0.01301757055, 0.0129304737772, -0,017106594595959]

Interceção: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0,608017146081

Tempo de execução acima da célula: 58,42 segundos

### <a name="random-forest-regression"></a>Regressão florestal aleatória

O código nesta secção mostra como treinar, avaliar e salvar uma regressão florestal aleatória que prevê o valor da gorjeta para os dados da viagem de táxi de NYC.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Tempo de execução acima da célula: 49,21 segundos

### <a name="gradient-boosting-trees-regression"></a>Regressão das árvores de gradiente

O código nesta secção mostra como treinar, avaliar e salvar um modelo de árvores impulsionadoras de gradiente que prevê o valor da gorjeta para os dados da viagem de táxi de NYC.

**Treinar e avaliar**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Tempo de execução acima da célula: 34,52 segundos

**Lote**

*tmp_results* está registado como uma tabela de Colmeia na célula anterior. Os resultados da tabela são a saída para o quadro de *dados sqlResults* para a trama. Aqui está o código

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Aqui está o código para traçar os dados usando o servidor Jupyter.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**SAÍDA:**

![Montantes reais vs-previstos](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Limpar objetos da memória

Utilize `unpersist()` para eliminar objetos em cache na memória.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>Salvar os modelos

Para consumir e obter um conjunto de dados independente descrito no [Tópico de Aprendizagem automática construído](spark-model-consumption.md) pelo Spark, é necessário copiar e colar estes nomes de ficheiros que contenham os modelos guardados criados aqui no caderno Consumption Jupyter. Aqui está o código para imprimir os caminhos para modelar ficheiros que precisa lá.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**SAÍDA**

logísticaRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

RandomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

Reforço daTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

ReforçotreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>O que se segue?

Agora que criou modelos de regressão e classificação com o Spark MlLib, está pronto para aprender a pontuar e avaliar estes modelos. O notebook avançado de pesquisa de dados e modelação mergulha mais profundamente na inclusão de validação cruzada, varredura de hiper-parâmetros e avaliação de modelos. 

**Consumo de modelos:** Para aprender a pontuar e avaliar os modelos de classificação e regressão criados neste tópico, consulte [Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

**Varredura de validação cruzada e hiperparímetro**: Consulte [a exploração e modelação avançadas de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando a validação cruzada e a varredura de hiper-parâmetros