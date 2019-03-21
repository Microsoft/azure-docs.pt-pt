---
title: Exploração de dados e modelação com o Spark - Team Data Science Process
description: Demonstra as capacidades de exploração e modelação de dados do toolkit Spark MLlib no Azure.
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
ms.openlocfilehash: 4ddcd2429ce1b7e44670b52a0a7b7494d0400af7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860980"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com o Spark

Estas instruções utilizam o Spark do HDInsight para fazer a exploração de dados e classificação binária e regressão tarefas de modelagem de uma amostra do NYC táxis viagem e se comportarão de conjunto de dados de 2013.  Ele explica-lhe os passos para o [processo de ciência de dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)ponto-a- ponto, utilizar um cluster do HDInsight Spark para blobs de processamento e do Azure para armazenar os dados e os modelos. O processo explora e visualiza dados trazidos de um Blob de armazenamento do Azure e, em seguida, prepara os dados para criar modelos preditivos. Estes modelos são de compilação usando o Kit de ferramentas do Spark MLlib para efetuar tarefas de modelagem de classificação binária e regressão.

* O **classificação binária** tarefa é prever se ou não uma dica é pago para a viagem. 
* O **regressão** tarefa é prever a quantidade de sugestão com base em outros recursos de dica. 

Os modelos que usamos incluem regressão logística e linear, florestas aleatórias e árvores de elevada gradação:

* [Regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo de regressão linear que utiliza um método stochastic Gradient gradiente descendente (SGD) e para Otimização e a funcionalidade de dimensionamento para prever as quantidades de sugestão pago. 
* [Regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou regressão "logit", é um modelo de regressão que pode ser utilizado quando a variável dependente é categórica para fazer a classificação de dados. LBFGS é um algoritmo de otimização de quasi-Newton que aproxima-se o algoritmo de Broyden – Fletcher – Goldfarb – Shanno (BFGS) com uma quantidade limitada de memória do computador e que é amplamente usado no machine learning.
* [Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são árvores de árvores de decisões.  Elas combinam muitos árvores de decisão para reduzir o risco de overfitting. Florestas aleatórias são usadas para classificação e regressão e podem lidar com recursos categóricos e podem ser estendidas para a definição de classificação multiclasses. Eles não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Florestas aleatórias são uma do mais bem-sucedidas modelos de machine learning para classificação e regressão.
* [Gradiente aumentado árvores](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são árvores de árvores de decisões. GBTs treinar árvores de decisão iterativamente para minimizar a uma função de perda. GBTs são utilizados para classificação e regressão e pode lidar com recursos categóricos, não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Eles também podem ser usados numa configuração de classificação de várias classes.

Os passos de modelação também contêm código que mostra como treinar, avaliar e guardar cada tipo de modelo. Python tem sido utilizado para a solução de código e para mostrar os gráficos relevantes.   

> [!NOTE]
> Embora o toolkit de Spark MLlib foi projetado para trabalhar em grandes conjuntos de dados, um exemplo relativamente pequeno (Mb de aproximadamente 30 com linhas de 170K, cerca de 0,1% do conjunto de dados original NYC) é utilizado aqui para sua comodidade. O exercício fornecido aqui é executado com eficiência (em cerca de 10 minutos) num cluster do HDInsight com 2 nós de trabalho. O mesmo código, com pequenas modificações, pode ser utilizado para processar conjuntos de dados maiores, com as modificações apropriadas para a colocação em cache os dados na memória e alterar o tamanho do cluster.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Precisa de uma conta do Azure e um Spark 1.6 (ou Spark 2.0) cluster do HDInsight para concluir estas instruções. Consulte a [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md) para obter instruções sobre como atender a esses requisitos. Esse tópico também contém uma descrição dos dados de táxis de 2013 NYC usados aqui e obter instruções sobre como executar o código a partir de um bloco de notas do Jupyter no cluster do Spark. 

## <a name="spark-clusters-and-notebooks"></a>Os clusters do Spark e blocos de notas
Passos de configuração e de código são fornecidos esta explicação passo a passo para utilizar um HDInsight Spark 1.6. Mas blocos de notas do Jupyter são fornecidos para os clusters do HDInsight Spark 1.6 e Spark 2.0. Uma descrição dos blocos de notas e links para eles são fornecidos na [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e o gerenciamento de cluster podem ser ligeiramente diferentes do que é mostrado aqui. Para sua comodidade, aqui estão os links para os blocos de notas do Jupyter para Spark 1.6 (a ser executado no kernel do pySpark, do servidor de notas do Jupyter) e o Spark 2.0 (a ser executado no kernel do pySpark3 do servidor de notas do Jupyter):

### <a name="spark-16-notebooks"></a>Blocos de notas do Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Fornece informações sobre como realizar a exploração, modelação e com diversos algoritmos de classificação de dados.

### <a name="spark-20-notebooks"></a>Blocos de notas do Spark 2.0
Tarefas de regressão e de classificação que são implementadas por meio de um cluster do Spark 2.0 são em blocos de notas separados e o bloco de notas de classificação utiliza um conjunto de dados diferente:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como efetuar a exploração de dados, de modelagem, e de classificação no Spark 2.0 clusters com a viagem de táxis de NYC e Europeia conjunto de dados descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este bloco de notas pode ser um ponto de partida para explorar rapidamente o código fornecidos para Spark 2.0. Para um bloco de notas mais detalhado analisa os dados de táxis de NYC, consulte o seguinte bloco de notas nesta lista. Consulte as notas seguinte nesta lista, comparam estes blocos de notas. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Este ficheiro mostra como realizar a preparação de dados (operações de Spark SQL e o pacote de dados), exploração, modelação e classificação usando a viagem de táxis de NYC e Europeia conjunto de dados descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Este ficheiro mostra como realizar a preparação de dados (operações de Spark SQL e o pacote de dados), exploração, modelação e classificação usando o dataset de saída de no tempo de companhia aérea bem conhecido de 2011 e 2012. Integrámos o conjunto de dados de companhia aérea com os dados de Meteorologia aeroporto (por exemplo, windspeed, temperatura, altitude etc.) antes da modelagem, para que estas funcionalidades de Meteorologia podem ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados de companhia aérea foi adicionado para os blocos de notas do Spark 2.0 para melhor ilustrar a utilização de algoritmos de classificação. Consulte as seguintes ligações para informações sobre a companhia aérea no tempo de conjunto de dados de saída e o conjunto de dados de Meteorologia:
> 
> - Dados de saída no tempo de companhia aérea: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dados meteorológicos de aeroporto: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Os blocos de notas do Spark 2.0 nos NYC táxis e companhia aérea voo atraso-conjuntos de dados podem demorar 10 minutos ou mais para ser executada (dependendo do tamanho do cluster do HDI). O primeiro bloco de notas na lista acima mostra muitos aspectos de exploração de dados, visualização e preparação de modelos de ML num bloco de notas que demora menos tempo a executar com o conjunto de dados do NYC objeto de amostragem de baixo, no qual os ficheiros de táxis e Europeia tenham sido previamente associado a um: [Spark2.0-pySpark3-Machine-Learning-data-Science-spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) este bloco de notas demora muito menor tempo a concluir (2 a 3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código fornecidos para o Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> As descrições abaixo estão relacionadas ao uso de Spark 1.6. Para versões de Spark 2.0, utilize os blocos de notas descrito e ligação acima. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Programa de configuração: localizações de armazenamento, bibliotecas e o contexto de Spark predefinido
O Spark é capaz de ler e escrever para BLOBs de armazenamento do Azure (também conhecido como WASB). Portanto, qualquer um dos seus dados existentes armazenados aqui podem ser processados com o Spark e os resultados novamente armazenados a ser WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificadas corretamente. O contentor predefinido ligado para o cluster do Spark pode ser referenciado com um início do caminho com: "wasb: / / /". Outros locais são referenciados por "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para localizações de armazenamento no WASB
O código de exemplo seguinte especifica a localização dos dados a ser lido e o caminho para o diretório de armazenamento de modelo ao qual o resultado de modelo é guardado:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotecas de importação
Configure também requer a importação de bibliotecas necessárias. Definir o contexto do spark e importe bibliotecas necessárias com o código a seguir:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de Spark predefinido e a magia do PySpark
PySpark kernels que são fornecidos com blocos de notas do Jupyter têm um contexto predefinido. Portanto, não é necessário definir o Spark ou do Hive contextos explicitamente antes de começar a trabalhar com a aplicação estiver a desenvolver. Desses contextos são disponíveis para si por predefinição. Desses contextos são:

* SC - para o Spark 
* kontext sqlContext - para o Hive

O kernel do PySpark fornece alguns predefinidas "magia", que são comandos especiais que pode chamar com % %. Existem dois desses comandos que são utilizados nestas amostras de código.

* **% % local** Especifica se o código nas linhas subsequentes está a ser executado localmente. Código tem de ser um código de Python válido.
* **% % sql -o <variable name>**  executa uma consulta do Hive contra o kontext sqlContext. Se o parâmetro -o passado, o resultado da consulta é mantido no % % contexto Python local como um Pandas DataFrame.

Para obter mais informações sobre os kernels de blocos de notas do Jupyter e predefinidos "magics" que fornecem, consulte [Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux clusters no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados do blob público
A primeira etapa no processo de ciência de dados é para ingestão de dados para ser analisado a partir de origens de onde é reside no seu ambiente de exploração e modelação de dados. O ambiente é o Spark nestas instruções. Esta secção contém o código para concluir uma série de tarefas:

* a amostra de dados seja modelada de ingestão
* o conjunto de dados de entrada (armazenado como um ficheiro. tsv) de leitura
* Formatar e limpar os dados
* criar e armazenar em cache objetos (RDDs ou quadros de dados) na memória
* Registe-o como uma tabela de temp no contexto de SQL.

Aqui está o código para ingestão de dados.

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

**SAÍDA:**

Tempo decorrido para executar acima célula: 51.72 segundos

## <a name="data-exploration--visualization"></a>Exploração de dados e visualização
Depois dos dados tem sido colocados em Spark, a próxima etapa no processo de ciência de dados é obter uma compreensão mais aprofundada dos dados por meio de exploração e visualização. Nesta secção, vamos examinar os dados de táxis através de consultas SQL e desenhar as variáveis de destino e os recursos potenciais para inspeção visual. Especificamente, vamos desenhar a frequência de contagens de passageiros em viagens de táxis, a frequência de quantidades de sugestão e como dicas variam consoante o valor do pagamento e o tipo.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Um histograma da frequências de contagem de passageiros no exemplo de viagens de táxis de desenho
Este código e trechos de código subsequentes utilizam "SQL Magic" para consultar o exemplo e mágica local para representar os dados.

* **"SQL Magic" (`%%sql`)** kernel do PySpark do HDInsight suporta consultas de HiveQL de fácil inline contra o kontext sqlContext. O (-s VARIABLE_NAME) argumento persistir o resultado da consulta SQL como um DataFrame Pandas no servidor do Jupyter. Isso significa que está disponível no modo de local.
* O  **`%%local` magia** é utilizada para executar código localmente no servidor do Jupyter, o que é o nó principal do HDInsight cluster. Normalmente, utiliza `%%local` mágica em conjunto com o `%%sql` mágico com o parâmetro -o. O parâmetro -o persistir o resultado da consulta SQL localmente e, em seguida, % % mágica local dispararia o próximo conjunto de Trecho de código para executar localmente em relação a saída das consultas SQL que é mantida localmente

O resultado é visualizado automaticamente depois de executar o código.

Esta consulta obtém as viagens por contagem de passageiros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Este código cria um quadro de dados local a partir da saída da consulta e desenha os dados. O `%%local` mágica cria um quadro de dados local, `sqlResults`, que pode ser utilizado para desenhar com matplotlib. 

> [!NOTE]
> Essa mágica PySpark é utilizada várias vezes nestas instruções. Se a quantidade de dados é grande, deve de exemplo para criar um quadro de dados que cabem na memória local.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Aqui está o código para desenhar as viagens por contagens de passageiros

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

**SAÍDA:**

![Frequência de viagem por contagem de passageiros](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pode selecionar entre os vários tipos diferentes de visualizações (tabela, gráfico de pizza, linha, área ou barra) ao utilizar o **tipo** botões de menu no bloco de notas. O gráfico de barras é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Desenhe um histograma da tip quantidades e como Gorjeta varia por quantidades de contagem e Europeia passageiro.
Utilize uma consulta SQL para dados de exemplo.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

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


Essa célula de código utiliza a consulta SQL para criar três gráficos os dados.

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


**SAÍDA:** 

![Distribuição de quantidade de sugestão](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Gorjeta por contagem de passageiros](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Gorjeta pela quantidade de Europeia](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparação de dados, transformação e engenharia para modelagem de recursos
Esta secção descreve e fornece o código para obter os procedimentos utilizados para preparar dados para utilização na modelagem de ML. Ele mostra como realizar as seguintes tarefas:

* Criar um novo recurso por horas de discretização em registos de tempo de tráfego
* Indexar e codificar funcionalidades categóricas
* Criar objetos com nome de ponto de entrada para as funções de ML
* Criar uma amostragem aleatória secundárias dos dados e dividi-lo em preparação e teste conjuntos
* Dimensionamento de recursos
* Objetos de cache na memória

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar um novo recurso por horas de discretização em registos de tempo de tráfego
Este código mostra como criar um novo recurso por horas de discretização em registos de tempo de tráfego e, em seguida, como colocar em cache o quadro de dados resultante na memória. Onde Datasets de distribuídos resilientes (RDDs) e quadros de dados são usados repetidamente, colocação em cache leva a mais tempos de execução. Da mesma forma, podemos armazenar em cache RDDs e quadros de dados em vários estágios no passo a passo. 

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

**SAÍDA:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexar e codificar categóricas funcionalidades de entrada para as funções de modelagem
Esta secção mostra como índice ou codificar categóricas funcionalidades de entrada para as funções de modelagem. A Modelagem e prever as funções de MLlib necessitam de funcionalidades com dados categóricos de entrada a ser indexados ou codificada antes de utilizar. Dependendo do modelo, terá de índice ou codificá-los de formas diferentes:  

* **Modelagem com base em árvore** requer categorias para ser codificado como valores numéricos (por exemplo, uma funcionalidade com três categorias pode ser codificada com 0, 1, 2). Isto é fornecido do MLlib [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) função. Esta função codifica uma coluna de cadeia de caracteres de etiquetas para uma coluna de índices de etiqueta ordenadas frequências de etiqueta. Embora indexado com valores numéricos para entrada e de processamento de dados, os algoritmos de árvore podem ser especificados para tratá-los adequadamente como categorias. 
* **Modelos de regressão Linear e de logística** necessitam de acesso frequente uma codificação, onde, por exemplo, uma funcionalidade com três categorias pode ser expandida para três colunas de funcionalidades, com cada que contêm 0 ou 1 consoante a categoria de uma observação. Fornece MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) função para fazer uma-hot codificação. Este codificador mapeia uma coluna de índices de etiqueta a uma coluna de vetores de binários, no máximo um único um valor. Esta codificação permite que os algoritmos que esperam que os recursos de com valores numéricos, como a regressão logística, possam ser aplicadas aos recursos categóricos.

Aqui está o código para indexar e codificar funcionalidades categóricas:

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

**SAÍDA:**

Tempo decorrido para executar acima célula: 1.28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos com nome de ponto de entrada para as funções de ML
Esta secção contém código que mostra como indexar dados categóricos texto como um tipo de dados do ponto de etiquetadas e codificá-lo para que ele pode ser usado para dar formação e teste de regressão logística de MLlib e outros modelos de classificação. Objetos point etiquetadas são formatados de forma que é necessária, como dados de entrada, pela maioria dos algoritmos de ML no MLlib com a conjuntos de dados de distribuídos resilientes (RDD). R [rotulado como ponto de](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, densa ou disperso, associado com uma etiqueta/resposta.  

Esta secção contém código que mostra como indexar dados categóricos texto como uma [rotulado como ponto de](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) escreva e codificá-lo para que ele pode ser usado para dar formação e teste de regressão logística de MLlib e outros modelos de classificação de dados. Objetos point etiquetadas são consiste numa etiqueta (variável de destino/resposta) e o vetor de recurso com a conjuntos de dados de distribuídos resilientes (RDD). Este formato é necessários como entrada para muitos algoritmos de ML MLlib.

Aqui está o código para indexar e codificar as funcionalidades de texto para classificação binária.

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


Aqui está o código para codificar e recursos de texto categóricos para análise de regressão linear de índice.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Criar uma amostragem aleatória secundárias dos dados e dividi-lo em preparação e teste conjuntos
Este código cria uma amostragem aleatória de dados (25% é utilizado aqui). Embora não seja necessária para este exemplo devido ao tamanho do conjunto de dados, vamos demonstrar como pode apresentar exemplos aqui para saber como utilizá-lo para o seu problema quando necessário. Quando os exemplos são grandes, isso pode economizar muito tempo enquanto os modelos de treinamento. Em seguida vamos dividir o exemplo numa parte de treinamento (75% aqui) e uma parte de teste (25% aqui) para classificação e modelação de regressão.

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

**SAÍDA:**

Tempo decorrido para executar acima célula: 0.24 segundos

### <a name="feature-scaling"></a>Dimensionamento de recursos
Dimensionamento de recursos, também conhecido como normalização de dados, forma, assegura que funcionalidades com valores amplamente disbursed são não fornecido excessiva pesar na função Objetiva. O código para a funcionalidade de dimensionamento utiliza a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para dimensionar os recursos a variância de unidade. Ele é fornecido pelo MLlib para utilização em regressão linear com stochastic Gradient gradiente descendente (SGD), um algoritmo popular para uma vasta gama de outros modelos de machine learning, como máquinas de vetores de suporte (SVM) ou de regularized regressões de formação.

> [!NOTE]
> Descobrimos que o algoritmo de LinearRegressionWithSGD ser sensível à funcionalidade de dimensionamento.
> 
> 

Aqui está o código para variáveis de dimensionamento para utilização com o algoritmo SGD regularized linear.

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

**SAÍDA:**

Tempo decorrido para executar acima célula: 13.17 segundos

### <a name="cache-objects-in-memory"></a>Objetos de cache na memória
O tempo decorrido para a preparação e teste de algoritmos de ML pode ser reduzido ao colocar em cache o quadro de dados de entrada objetos utilizados para a classificação, regressão e dimensionados de funcionalidades.

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

**SAÍDA:** 

Tempo decorrido para executar acima célula: segundos de 0,15

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever se ou não uma dica é pago com modelos de classificação binária
Esta secção mostra como utilizar três modelos para a tarefa de classificação binária de prever se ou não uma dica é pago por uma viagem de táxis. Os modelos apresentados são:

* Regressão logística regularized 
* Modelo de floresta aleatório
* Gradação árvores adaptativo

Cada modelo de criação de seção de código é dividido em passos: 

1. **Treinamento de modelo** dados com um conjunto de parâmetros
2. **Avaliação de modelo** num conjunto de dados de teste com a métrica
3. **A guardar modelo** no blob para o consumo futuro

### <a name="classification-using-logistic-regression"></a>Classificação de regressão logística de com
O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se ou não uma dica é pago por uma viagem no NYC táxis viagem e Europeia conjunto de dados.

**Preparar o modelo de regressão logística usando CV e varrimento de hiper-parâmetros**

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


**SAÍDA:** 

Coeficientes: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e - 05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intercepção:-0.0111216486893

Tempo decorrido para executar acima célula: 14.43 segundos

**Avaliar o modelo de classificação binária com métricas padrão**

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

**SAÍDA:** 

Área de PR = 0.985297691373

Área de ROC = 0.983714670256

Estatísticas de resumidas

Precisão = 0.984304060189

Lembre-se = 0.984304060189

F1 Pontuação = 0.984304060189

Tempo decorrido para executar acima célula: 57.61 segundos

**Representar a curva cor MULTICLASSE.**

O *predictionAndLabelsDF* está registado como uma tabela *tmp_results*, a célula anterior. *tmp_results* pode ser utilizado para fazer consultas e resultados de saída para o quadro de dados sqlResults para desenhar. Aqui está o código.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Aqui está o código para fazer previsões e a curva cor MULTICLASSE de desenho.

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


**SAÍDA:**

![Regressão logística ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação de floresta aleatório
O código nesta secção mostra como treinar, avaliar e guardar um modelo de floresta aleatória que preveja se ou não uma dica é pago por uma viagem no NYC táxis viagem e Europeia conjunto de dados.

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

**SAÍDA:**

Área de ROC = 0.985297691373

Tempo decorrido para executar acima célula: 31.09 segundos

### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores adaptativo gradação
O código nesta secção mostra como treinar, avaliar e guardar um modelo de árvores de adaptativo gradação que preveja se ou não uma dica é pago por uma viagem em viagem de táxis NYC e se comportarão de conjunto de dados.

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


**SAÍDA:**

Área de ROC = 0.985297691373

Tempo decorrido para executar acima célula: 19.76 segundos

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Prever as quantidades de sugestão de viagens de táxis com modelos de regressão
Esta secção mostra como utilizar três modelos para a tarefa de regressão de prever a quantidade de ponta pagavam por uma viagem de táxis com base em outros recursos de dica. Os modelos apresentados são:

* Regressão linear regularized
* Floresta aleatória
* Gradação árvores adaptativo

Esses modelos foram descritos na introdução. Cada modelo de criação de seção de código é dividido em passos: 

1. **Treinamento de modelo** dados com um conjunto de parâmetros
2. **Avaliação de modelo** num conjunto de dados de teste com a métrica
3. **A guardar modelo** no blob para o consumo futuro

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD
O código nesta secção mostra como usar recursos dimensionados para preparar uma regressão linear que utiliza descendente gradação stochastic gradient (SGD) para a Otimização e como classificar, avaliar e guardar o modelo no armazenamento de Blobs do Azure (WASB).

> [!TIP]
> Na nossa experiência, podem existir problemas com a convergência dos modelos de LinearRegressionWithSGD e parâmetros têm de ser alterado/otimizada com cuidado para obtenção de um modelo válido. Dimensionamento significativamente de variáveis de ajuda com a convergência. 
> 
> 

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

**SAÍDA:**

Coeficientes: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,- 0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Interceptar: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Tempo decorrido para executar acima célula: 58.42 segundos

### <a name="random-forest-regression"></a>Regressão de floresta de Random
O código nesta secção mostra como treinar, avaliar e guardar uma regressão de floresta aleatória que prevê Gorjeta para os dados de viagens de táxis NYC.

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

**SAÍDA:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Tempo decorrido para executar acima célula: 49.21 segundos

### <a name="gradient-boosting-trees-regression"></a>Gradação regressão de árvores adaptativo
O código nesta secção mostra como treinar, avaliar e guardar um modelo de árvores de adaptativo gradação que prevê Gorjeta para os dados de viagens de táxis NYC.

**Dar formação e avaliar**

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

**SAÍDA:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Tempo decorrido para executar acima célula: 34.52 segundos

**Plot**

*tmp_results* está registado como uma tabela do Hive a célula anterior. Resultados da tabela são de saída para o *sqlResults* quadro de dados para representar. Aqui está o código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Aqui está o código para desenhar os dados a utilizar o servidor do Jupyter.

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


**SAÍDA:**

![Real-vs-previsto-sugestão-quantidades](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Limpar objetos da memória
Utilize `unpersist()` eliminar objetos em cache na memória.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Localizações de armazenamento de registo dos modelos para consumo e de classificação
Para consumir e classificar um conjunto de dados independente descrito a [pontuação e avaliar modelos criados com o Spark machine learning](spark-model-consumption.md) tópico, tem de copiar e colar esses nomes de ficheiro que contém os modelos guardados criados aqui para o consumo Bloco de notas do Jupyter. Aqui está o código para imprimir os caminhos para ficheiros de modelo, que precisa aí.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SAÍDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Passos seguintes?
Agora que criou os modelos de regressão e de classificação com o Spark MlLib, está pronto para aprender a pontuação e avaliar esses modelos. A exploração de dados avançada e modelação de bloco de notas se aprofunda-se nos incluindo a validação cruzada, hyper varrimento, parâmetro e avaliação de modelo. 

**Consumo de modelo:** Para saber como classificar e avaliar os modelos de classificação e regressão criados neste tópico, veja [pontuação e avaliar modelos de aprendizagem automática criados com o Spark](spark-model-consumption.md).

**A validação cruzada e varrimento de hiper-parâmetros**: Ver [avançada de exploração de dados e modelagem com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser treinado varrimento de validação cruzada e de hyper-parâmetro a utilizar

