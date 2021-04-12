---
title: Exploração e modelação avançada de dados com Spark - Processo de Ciência de Dados de Equipa
description: Use o HDInsight Spark para fazer a exploração de dados e treinar modelos de classificação e regressão binárias utilizando a validação cruzada e a otimização do hiperparaímetro.
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
ms.openlocfilehash: 37f0b48190bcbad7ab190da1d690a1a0485501a1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259324"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Exploração e modelação avançada de dados com o Spark

Este walkthrough usa HDInsight Spark para fazer a exploração de dados e treinar modelos de classificação e regressão binária usando a otimização de validação cruzada e hiperparametr numa amostra da viagem de táxi de NYC e do conjunto de dados de tarifa 2013. Percorre os passos do Processo de Ciência de [Dados](./index.yml), de ponta a ponta, utilizando um cluster HDInsight Spark para processamento e bolhas Azure para armazenar os dados e os modelos. O processo explora e visualiza os dados trazidos de uma Bolha de Armazenamento Azure e, em seguida, prepara os dados para construir modelos preditivos. Python tem sido usado para codificar a solução e mostrar os enredos relevantes. Estes modelos são construídos utilizando o kit de ferramentas Spark MLlib para fazer tarefas de classificação binária e de modelação de regressão. 

* A tarefa **de classificação binária** é prever se uma gorjeta é ou não paga pela viagem. 
* A tarefa **de regressão** é prever a quantidade da ponta com base em outras características da ponta. 

Os passos de modelação também contêm código que mostra como treinar, avaliar e salvar cada tipo de modelo. O tópico abrange parte do mesmo terreno que a [exploração de Dados e modelação com](spark-data-exploration-modeling.md) o tema Spark. Mas é mais "avançado" na medida em que também usa a validação cruzada com varredura de hiperparímetro para treinar modelos de classificação e regressão idealmente precisos. 

**A validação cruzada (CV)** é uma técnica que avalia o quão bem um modelo treinado num conhecido conjunto de dados generaliza para prever as características dos conjuntos de dados em que não foi treinado.  Uma implementação comum usada aqui é dividir um conjunto de dados em dobras K e, em seguida, treinar o modelo de uma forma redonda em todas as dobras. Avalia-se a capacidade do modelo de previsão exata quando testada contra o conjunto de dados independente desta dobra não utilizada para treinar o modelo.

**A otimização do hiperparímetro** é o problema de escolher um conjunto de hiperparímetros para um algoritmo de aprendizagem, geralmente com o objetivo de otimizar uma medida do desempenho do algoritmo num conjunto de dados independente. **Os hiperparmetros** são valores que devem ser especificados fora do procedimento de treino do modelo. As suposições sobre estes valores podem ter impacto na flexibilidade e precisão dos modelos. As árvores de decisão têm hiperparímetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. As máquinas de vetor de suporte (SVMs) requerem a definição de um termo de penalização por classificação errada. 

Uma forma comum de realizar a otimização do hiperparímetro usada aqui é uma pesquisa de grelha, ou uma **varredura de parâmetros.** Esta pesquisa passa por um subconjunto do espaço hiperparímetro para um algoritmo de aprendizagem. A validação cruzada pode fornecer uma métrica de desempenho para resolver os resultados ideais produzidos pelo algoritmo de pesquisa da grelha. O CV utilizado com a varredura de hiperparímetros ajuda a limitar problemas como a adaptação excessiva de um modelo aos dados de formação, de modo a que o modelo mantenha a capacidade de aplicar ao conjunto geral de dados a partir do qual os dados de formação foram extraídos.

Os modelos que usamos incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas por gradiente:

* [A regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) é um modelo linear de regressão que utiliza um método de descida de gradiente estocástico (SGD) e para otimização e escala de recursos para prever os valores da gorjeta pagos. 
* [Regressão logística com REgressão LBFGS](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html
) ou "logit", é um modelo de regressão que pode ser usado quando a variável dependente é categórica para fazer classificação de dados. LBFGS é um algoritmo de otimização quase Newton que se aproxima do algoritmo Broyden-Fletcher-Goldfarb-Shanno (BFGS) usando uma quantidade limitada de memória de computador e que é amplamente utilizado na aprendizagem automática.
* [Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são conjuntos de árvores de decisão.  Combinam muitas árvores de decisão para reduzir o risco de sobremontagem. As florestas aleatórias são usadas para regressão e classificação e podem lidar com características categóricas e podem ser estendidas à definição de classificação multiclasse. Não necessitam de escala de recursos e são capazes de capturar não linearidades e interações de recurso. As florestas aleatórias são um dos modelos de aprendizagem automática mais bem sucedidos para classificação e regressão.
* [As árvores de gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) são conjuntos de árvores de decisão. GBTS treina as árvores de decisão iterativamente para minimizar uma função de perda. O GBTS é utilizado para regressão e classificação e pode lidar com funcionalidades categóricas, não requer escala de recursos e são capazes de capturar não linearidades e interações de recurso. Também podem ser usados numa definição de classificação multiclasse.

Exemplos de modelação utilizando varredura cv e hiperparameter são mostrados para o problema de classificação binária. Exemplos mais simples (sem varreduras de parâmetros) são apresentados no tópico principal para tarefas de regressão. Mas no apêndice, a validação utilizando rede elástica para regressão linear e CV com varredura de parâmetros usando para regressão florestal aleatória também são apresentadas. A **rede elástica** é um método regularizado de regressão para a montagem de modelos lineares de regressão que combina linearmente as métricas L1 e L2 como penalizações dos métodos do [laço](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) e da [crista.](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Embora o kit de ferramentas Spark MLlib tenha sido concebido para funcionar em grandes conjuntos de dados, uma amostra relativamente pequena (~30 Mb usando 170K linhas, cerca de 0,1% do conjunto de dados original de NYC) é usada aqui para conveniência. O exercício aqui dado funciona eficientemente (em cerca de 10 minutos) num cluster HDInsight com 2 nós de trabalhadores. O mesmo código, com pequenas modificações, pode ser usado para processar conjuntos de dados maiores, com modificações adequadas para caching dados na memória e alteração do tamanho do cluster.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Configuração: Agrupamentos de faíscas e cadernos
Os passos de configuração e o código são fornecidos neste walkthrough para a utilização de um HDInsight Spark 1.6. Mas os cadernos Jupyter são fornecidos tanto para os clusters HDInsight Spark 1.6 como para o Spark 2.0. Uma descrição dos cadernos e ligações a eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do mostrado aqui. Por conveniência, aqui estão as ligações com os cadernos Jupyter para Spark 1.6 e 2.0 a serem executados no núcleo pyspark do servidor Jupyter Notebook:

### <a name="spark-16-notebooks"></a>Spark 1.6 cadernos

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Inclui tópicos em #1 de cadernos e desenvolvimento de modelos usando afinação hiperparaítrâmea e validação cruzada.

### <a name="spark-20-notebooks"></a>Spark 2.0 cadernos

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar a exploração de dados, modelação e pontuação em clusters Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuração: locais de armazenamento, bibliotecas e o contexto de faísca predefinido
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**SAÍDA**

datatime.datetime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importar bibliotecas
Importar bibliotecas necessárias com o seguinte código:

```python
# LOAD PYSPARK LIBRARIES
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

Para obter mais informações sobre os núcleos para os cadernos Jupyter e as "magias" predefinidas que eles fornecem, consulte [Kernels disponível para cadernos Jupyter com clusters HDInsight Spark Linux em HDInsight.](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados da bolha pública:
O primeiro passo no processo de ciência de dados é ingerir os dados a serem analisados a partir de fontes onde reside no seu ambiente de exploração e modelação de dados. Este ambiente é Spark nesta passagem. Esta secção contém o código para completar uma série de tarefas:

* ingerir a amostra de dados a ser modelada
* ler no conjunto de dados de entrada (armazenado como um ficheiro .tsv)
* formato e limpar os dados
* criar e cache objetos (RDDs ou quadros de dados) na memória
* registe-o como uma tabela temporária no contexto SQL.

Aqui está o código para a ingestão de dados.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

Tempo de execução acima da célula: 276,62 segundos

## <a name="data-exploration--visualization"></a>Visualização & de exploração de dados
Uma vez que os dados tenham sido trazidos para a Spark, o próximo passo no processo de ciência de dados é obter uma compreensão mais profunda dos dados através da exploração e visualização. Nesta secção, examinamos os dados do táxi usando consultas SQL e traçamos as variáveis-alvo e as funcionalidades prospetivas para inspeção visual. Especificamente, traçamos a frequência das contagens de passageiros em viagens de táxi, a frequência dos valores das gorjetas, e como as gorjetas variam por valor de pagamento e tipo.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Traçar um histograma de frequências de contagem de passageiros na amostra de viagens de táxi
Este código e os subsequentes snippets usam magia SQL para consultar a amostra e a magia local para traçar os dados.

* **MAGIA SQL ( `%%sql` )** O kernel HDInsight PySpark suporta consultas de HiveQL fáceis em linha contra o sqlContext. O argumento (-o VARIABLE_NAME) persiste na saída da consulta SQL como um DataFrame pandas no servidor Jupyter. Isto significa que está disponível no modo local.
* A **`%%local` magia** é usada para executar código localmente no servidor Jupyter, que é o headnode do cluster HDInsight. Normalmente, usa-se `%%local` magia depois de a magia ser usada para fazer uma `%%sql -o` consulta. O parâmetro -o persistiria a saída da consulta SQL localmente. Em seguida, a `%%local` magia desencadeia o próximo conjunto de cortes de código para correr localmente contra a saída das consultas SQL que tem sido persistido localmente. A saída é visualizada automaticamente depois de executar o código.

Esta consulta recupera as viagens por contagem de passageiros. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Este código cria um quadro de dados local a partir da saída de consulta e traça os dados. A `%%local` magia cria um quadro de dados local, que pode ser usado para conspirar com `sqlResults` matplotlib. 

<!-- -->

> [!NOTE]
> Esta magia PySpark é usada várias vezes nesta passagem. Se a quantidade de dados for grande, deve recolher amostras para criar um quadro de dados que possa caber na memória local.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Aqui está o código para traçar as viagens por contagens de passageiros

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**SAÍDA**

![Frequência de viagens por contagem de passageiros](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Pode selecionar entre vários tipos de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões de menu **Tipo** no caderno. O enredo do Bar é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Defina um histograma de montantes de gorjetas e como o valor da gorjeta varia conso por conta de passageiros e quantidades de tarifas.
Use uma consulta SQL para recolher dados de amostra..

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**SAÍDA:** 

![Distribuição de montante de gorjeta](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Valor da gorjeta por contagem de passageiros](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Valor da gorjeta por tarifa](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Engenharia de recursos, transformação e preparação de dados para modelação
Esta secção descreve e fornece o código para os procedimentos utilizados para preparar dados para utilização na modelação ML. Mostra como fazer as seguintes tarefas:

* Criar uma nova funcionalidade dividindo horas em caixotes do tempo de tráfego
* Índice e codificação em brasa características categóricas
* Criar objetos de ponto marcados para entrada em funções ML
* Criar uma subesmalte aleatória dos dados e dividi-lo em conjuntos de treino e teste
* Dimensionamento de funcionalidades
* Objetos de cache na memória

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Criar uma nova funcionalidade dividindo os tempos de tráfego em caixotes
Este código mostra como criar uma nova funcionalidade dividindo os tempos de tráfego em caixotes e, em seguida, como cache o quadro de dados resultante na memória. O caching leva a um melhor tempo de execução onde os conjuntos de dados distribuídos resilientes (RDDs) e os quadros de dados são usados repetidamente. Então, cache RDDs e data-frames em várias fases nesta passagem.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**SAÍDA**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Índice e características categóricas de codificação de um quente
Esta secção mostra como indexar ou codificar funcionalidades categóricas para a entrada nas funções de modelação. As funções de modelação e previsão do MLlib exigem que as funcionalidades com dados de entrada categóricos sejam indexadas ou codificadas antes da utilização. 

Dependendo do modelo, é necessário indexá-los ou codificá-los de diferentes formas. Por exemplo, os modelos de Regressão Logística e Linear requerem codificação de um só hot, onde, por exemplo, uma característica com três categorias pode ser expandida em três colunas de recurso, com cada uma contendo 0 ou 1 dependendo da categoria de observação. A MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para fazer uma codificação única. Este codificadora mapeia uma coluna de índices de etiquetas para uma coluna de vetores binários, com no máximo um único valor único. Esta codificação permite que algoritmos que esperem características numéricas valorizadas, como a regressão logística, sejam aplicadas a características categóricas.

Aqui está o código para indexar e codificar características categóricas:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

**SAÍDA**

Tempo de execução acima da célula: 3,14 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos de ponto marcados para entrada em funções ML
Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto marcado e como codificá-lo. Esta transformação prepara dados de texto para serem usados para treinar e testar a regressão logística da MLlib e outros modelos de classificação. Os objetos de pontos rotulados são conjuntos de dados distribuídos resilientes (RDD) formatados de uma forma que é necessária como dados de entrada pela maioria dos algoritmos ML em MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou escasso, associado a um rótulo/resposta.

Aqui está o código para indexar e codificar as características de texto para a classificação binária.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Este código cria uma amostragem aleatória dos dados (25% é usado aqui). Embora não seja necessário para este exemplo devido ao tamanho do conjunto de dados, demonstramos como pode recolher amostras dos dados aqui. Então sabe como usá-lo para o seu próprio problema, se necessário. Quando as amostras são grandes, a amostragem pode economizar tempo significativo enquanto treina os modelos. Em seguida, dividimos a amostra numa parte de formação (75% aqui) e numa parte de teste (25% aqui) para usar na classificação e na modelação de regressão.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

**SAÍDA**

Tempo de execução acima da célula: 0,31 segundos

### <a name="feature-scaling"></a>Dimensionamento de funcionalidades
O dimensionamento de recursos, também conhecido como normalização de dados, assegura que as características com valores amplamente desembolsados não recebem um peso excessivo na função objetiva. O código de escala de funcionalidades utiliza o [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html
) para escalar as funcionalidades para a variação da unidade. É fornecido pela MLlib para utilização em regressão linear com Descida de Gradiente Estocástico (SGD). SGD é um algoritmo popular para treinar uma ampla gama de outros modelos de aprendizagem automática, tais como regressões regularizadas ou máquinas de vetores de suporte (SVM).   

> [!TIP]
> Descobrimos que o algoritmo LinearRegressionWithSGD é sensível ao dimensionamento de recursos.   
> 
> 

Aqui está o código para escalar variáveis para uso com o algoritmo linear regularizado SGD.

```python
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

**SAÍDA**

Tempo de execução acima da célula: 11,67 segundos

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

**SAÍDA** 

Tempo de execução acima da célula: 0,13 segundos

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever se uma gorjeta é ou não paga com modelos de classificação binário
Esta secção mostra como usar três modelos para a tarefa de classificação binária de prever se uma gorjeta é ou não paga para uma viagem de táxi. Os modelos apresentados são:

* Regressão logística 
* Floresta aleatória
* Árvores de aumento de gradiente

Cada secção de código de construção de modelos é dividida em passos: 

1. **Dados de formação de modelo** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em blob para consumo futuro

Mostramos como fazer validação cruzada (CV) com o parâmetro varrendo de duas maneiras:

1. Usando código personalizado **genérico** que pode ser aplicado a qualquer algoritmo em MLlib e a qualquer conjunto de parâmetros em um algoritmo. 
2. Utilizando a **função de gasoduto pySpark CrossValidator**. CrossValidator tem algumas limitações para Spark 1.5.0: 
   
   * Os modelos de gasoduto não podem ser salvos ou persistidos para consumo futuro.
   * Não pode ser utilizado para cada parâmetro de um modelo.
   * Não pode ser usado para todos os algoritmos MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Validação transversal genérica e varredura de hiperparímetro usada com o algoritmo de regressão logística para classificação binária
O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [o LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é ou não paga por uma viagem no conjunto de dados de táxi e tarifas de NYC. O modelo é treinado usando validação cruzada (CV) e varredura de hiperparímetro implementada com código personalizado que pode ser aplicado a qualquer um dos algoritmos de aprendizagem em MLlib.   

<!-- -->

> [!NOTE]
> A execução deste código CV personalizado pode demorar vários minutos.

<!-- -->

**Treine o modelo de regressão logística usando cv e varredura de hiperparímetro**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

Coeficientes: [0.0082065285375, -0.022365576104, -0.0183812028036, -3.48124578069e-05, -0.0024764694723, -0.00165897881503, 0.06753948373228, -0.1182313101, -0.32460912762, -0,204549780032, -1.36499216354, 0.59108507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.3.00439726852, -3.3.3.0043972685, -3.3.0043972685, -3.3.3.3.00439726852, 3.46567827545, -3.3.3.004397268 51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0,054518719222]

Interceção: -0.0111216486893

Tempo de execução acima da célula: 14,43 segundos

**Avaliar o modelo de classificação binária com métricas padrão**

O código desta secção mostra como avaliar um modelo de regressão logística contra um conjunto de dados de teste, incluindo um enredo da curva ROC.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

Área em RP = 0.985336538462

Área em ROC = 0.983383274312

Estatísticas sumárias

Precisão = 0.984174341679

Lembre-se = 0.984174341679

F1 Pontuação = 0.984174341679

Tempo de execução acima da célula: 2,67 segundos

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

**SAÍDA**

![Curva ROC de regressão logística para abordagem genérica](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Persistir modelo numa bolha para consumo futuro**

O código desta secção mostra como salvar o modelo de regressão logística para consumo.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**SAÍDA**

Tempo de execução acima da célula: 34,57 segundos

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Utilize a função de gasoduto CrossValidator da MLlib com o modelo de regressão logística (regressão elástica)
O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [o LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é ou não paga por uma viagem no conjunto de dados de táxi e tarifas de NYC. O modelo é treinado com validação cruzada (CV) e varredura de hiperparímetro implementada com a função de pipeline MLlib CrossValidator para CV com varredura de parâmetros.   

<!-- -->

> [!NOTE]
> A execução deste código MLlib CV pode demorar vários minutos.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**SAÍDA**

Tempo de execução acima da célula: 107,98 segundos

**Traçar a curva ROC.**

A *previsão ElabelsDF* está registada como uma tabela, *tmp_results,* na célula anterior. *tmp_results* pode ser usado para fazer consultas e resultados de saída no sqlResults data-frame para a trama. Aqui está o código.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Aqui está o código para traçar a curva ROC.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

**SAÍDA**

![Curva ROC de regressão logística utilizando crossvalidator da MLlib](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação florestal aleatória
O código nesta secção mostra como treinar, avaliar e salvar uma regressão florestal aleatória que prevê se uma gorjeta é ou não paga por uma viagem na viagem de táxi de NYC e conjunto de dados de tarifas.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

**SAÍDA**

Área sob ROC = 0.985336538462

Tempo de execução acima da célula: 26,72 segundos

### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores de aumento de gradiente
O código nesta secção mostra como treinar, avaliar e salvar um modelo de árvores impulsionadoras de gradiente que prevê se uma gorjeta é ou não paga para uma viagem na viagem de táxi de NYC e conjunto de dados de tarifas.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

**SAÍDA**

Área sob ROC = 0.985336538462

Tempo de execução acima da célula: 28,13 segundos

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prever o valor da ponta com modelos de regressão (não utilizando CV)
Esta secção mostra como usar três modelos para a tarefa de regressão: prever o valor da gorjeta paga por uma viagem de táxi com base em outras características da ponta. Os modelos apresentados são:

* Regressão linear regularizada
* Floresta aleatória
* Árvores de aumento de gradiente

Estes modelos foram descritos na introdução. Cada secção de código de construção de modelos é dividida em passos: 

1. **Dados de formação de modelo** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em blob para consumo futuro   

<!-- -->

> [!NOTE] 
> A validação cruzada não é utilizada com os três modelos de regressão nesta secção, uma vez que esta foi mostrada em detalhe para os modelos de regressão logística. Um exemplo que mostra como usar o CV com Rede Elástica para regressão linear é fornecido no apêndice deste tópico.

<!-- -->

<!-- -->

> [!NOTE] 
> Na nossa experiência, pode haver problemas com a convergência dos modelos LinearRegressionWithSGD, e os parâmetros precisam de ser alterados/otimizados cuidadosamente para obter um modelo válido. A escala de variáveis ajuda significativamente na convergência. A regressão líquida elástica, mostrada no apêndice a este tópico, também pode ser usada em vez de LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD
O código nesta secção mostra como usar funcionalidades em escala para treinar uma regressão linear que utiliza a descida estocástica de gradiente (SGD) para otimização, e como marcar, avaliar e salvar o modelo no Azure Blob Storage (WASB).

> [!TIP]
> Na nossa experiência, pode haver problemas com a convergência dos modelos LinearRegressionWithSGD, e os parâmetros precisam de ser alterados/otimizados cuidadosamente para obter um modelo válido. A escala de variáveis ajuda significativamente na convergência.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

Coeficientes: [0.014170753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.31254147152, 0.360296120645, 0.012079566092, -0.0045649858241, -0.08982285051777, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.00791124681938, -00.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.001467664882502]

Interceção: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Tempo de execução acima da célula: 38,62 segundos

### <a name="random-forest-regression"></a>Regressão florestal aleatória
O código nesta secção mostra como treinar, avaliar e salvar um modelo florestal aleatório que prevê o valor da gorjeta para os dados da viagem de táxi de NYC.   

<!-- -->

> [!NOTE]
> A validação cruzada com a varredura de parâmetros utilizando código personalizado é fornecida no apêndice.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**SAÍDA**

RMSE = 0.931981967875

R-sqr = 0,733445485802

Tempo de execução acima da célula: 25,98 segundos

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Tempo de execução acima da célula: 20,9 segundos

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Montantes reais vs-previstos](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Apêndice: Tarefas adicionais de regressão utilizando validação cruzada com varreduras de parâmetros
Este apêndice contém código que mostra como fazer CV usando rede elástica para regressão linear e como fazer CV com varredura de parâmetros usando código personalizado para regressão florestal aleatória.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Validação cruzada utilizando rede elástica para regressão linear
O código desta secção mostra como fazer validação cruzada utilizando rede elástica para regressão linear e como avaliar o modelo em função dos dados de teste.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

Tempo de execução acima da célula: 161,21 segundos

**Avaliar com métrica R-SQR**

*tmp_results* está registado como uma tabela de Colmeia na célula anterior. Os resultados da tabela são a saída para o quadro de *dados sqlResults* para a trama. Aqui está o código

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Aqui está o código para calcular R-sqr.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**SAÍDA**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Validação cruzada com varredura de parâmetros usando código personalizado para regressão florestal aleatória
O código desta secção mostra como fazer a validação cruzada com a varredura de parâmetros utilizando código personalizado para regressão florestal aleatória e como avaliar o modelo contra os dados de teste.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**SAÍDA**

RMSE = 0,906972198262

R-sqr = 0.740751197012

Tempo de execução acima da célula: 69,17 segundos

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Limpe objetos da memória e imprima localizações do modelo
Utilize `unpersist()` para eliminar objetos em cache na memória.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**SAÍDA**

PythonRDD[122] em RDD em PythonRDD.scala: 43

**Caminho de saída para modelar ficheiros a serem usados no caderno de consumo. ** Para consumir e obter um conjunto de dados independente, é necessário copiar e colar estes nomes de ficheiros no "Caderno de Consumo".

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**SAÍDA**

logísticaRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

RandomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

Reforço daTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

ReforçotreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>O que se segue?
Agora que criou modelos de regressão e classificação com o Spark MlLib, está pronto para aprender a pontuar e avaliar estes modelos.

**Consumo de modelos:** Para aprender a pontuar e avaliar os modelos de classificação e regressão criados neste tópico, consulte [Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)
