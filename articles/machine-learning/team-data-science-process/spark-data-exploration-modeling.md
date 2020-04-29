---
title: Exploração e modelação de dados com Spark - Team Data Science Process
description: Mostra as capacidades de exploração e modelação de dados do kit de ferramentas Spark MLlib no Azure.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76718638"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com o Spark

Este walkthrough utiliza a HDInsight Spark para fazer tarefas de exploração de dados e classificação binária e modelação de regressão numa amostra da viagem de táxi de NYC e conjunto de dados de tarifa 2013.  Percorre-o através dos passos do Processo de Ciência de [Dados,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)de ponta a ponta, utilizando um cluster HDInsight Spark para processamento e bolhas Azure para armazenar os dados e os modelos. O processo explora e visualiza os dados trazidos de um Blob de Armazenamento Azure e, em seguida, prepara os dados para construir modelos preditivos. Estes modelos são construídos usando o kit de ferramentas Spark MLlib para fazer tarefas de classificação binária e modelação de regressão.

* A tarefa **de classificação binária** é prever se uma gorjeta é ou não paga para a viagem. 
* A tarefa de **regressão** é prever a quantidade da ponta com base em outras características da ponta. 

Os modelos que usamos incluem regressão logística e linear, florestas aleatórias e árvores reforçadas de gradiente:

* [A regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo linear de regressão que utiliza um método de Descida De Gradiente Estocástico (SGD) e para otimização e dimensionamento de recursos para prever os valores da ponta pagos. 
* [A regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou regressão "logit", é um modelo de regressão que pode ser usado quando a variável dependente é categórica para fazer a classificação de dados. LBFGS é um algoritmo de otimização quase Newton que aproxima o algoritmo Broyden-Fletcher-Goldfarb-Shanno (BFGS) usando uma quantidade limitada de memória de computador e que é amplamente utilizado na aprendizagem automática.
* [Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são conjuntos de árvores de decisão.  Combinam muitas árvores de decisão para reduzir o risco de excesso de adaptação. As florestas aleatórias são usadas para regressão e classificação e podem lidar com características categóricas e podem ser estendidas à definição de classificação multiclasse. Não necessitam de dimensionamento de funcionalidades e são capazes de capturar não linearidades e interações de características. As florestas aleatórias são um dos modelos de aprendizagem automática mais bem sucedidos para a classificação e regressão.
* [As árvores reforçadas gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) são conjuntos de árvores de decisão. A decisão do comboio GBTS árvores iterativamente para minimizar uma função de perda. O GBTS é utilizado para regressão e classificação e pode lidar com características categóricas, não requer dimensionamento de funcionalidades e é capaz de capturar não linearidades e interações de recursos. Também podem ser usados numa definição de classificação multiclasse.

As etapas de modelação também contêm código que mostra como treinar, avaliar e salvar cada tipo de modelo. Python tem sido usado para codificar a solução e para mostrar os enredos relevantes.   

> [!NOTE]
> Embora o conjunto de ferramentas Spark MLlib tenha sido concebido para funcionar em grandes conjuntos de dados, uma amostra relativamente pequena (~30 Mb usando linhas de 170K, cerca de 0,1% do conjunto de dados original de NYC) é usada aqui para conveniência. O exercício aqui dado funciona de forma eficiente (em cerca de 10 minutos) num cluster HDInsight com 2 nós de trabalhador. O mesmo código, com pequenas modificações, pode ser utilizado para processar conjuntos de dados maiores, com modificações adequadas para dados de cache na memória e alteração do tamanho do cluster.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Você precisa de uma conta Azure e um cluster Spark 1.6 (ou Spark 2.0) HDInsight para completar este walkthrough. Consulte a [visão geral da Ciência dos Dados utilizando a Spark on Azure HDInsight](spark-overview.md) para obter instruções sobre como satisfazer estes requisitos. Este tópico também contém uma descrição dos dados de táxi de NYC 2013 usados aqui e instruções sobre como executar código a partir de um caderno Jupyter no cluster Spark. 

## <a name="spark-clusters-and-notebooks"></a>Aglomerados de faíscas e cadernos
Os passos e códigode configuração são fornecidos neste walkthrough para a utilização de uma Faísca HDInsight 1.6. Mas os cadernos Jupyter são fornecidos para os clusters HDInsight Spark 1.6 e Spark 2.0. Uma descrição dos cadernos e links para eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do que é mostrado aqui. Por conveniência, aqui estão os links para os cadernos Jupyter para Spark 1.6 (a ser executado no núcleo pySpark do servidor Jupyter Notebook) e Spark 2.0 (a ser executado no núcleo pySpark3 do servidor Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Cadernos Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Fornece informações sobre como realizar a exploração de dados, modelação e pontuação com vários algoritmos diferentes.

### <a name="spark-20-notebooks"></a>Cadernos Spark 2.0
As tarefas de regressão e classificação que são implementadas usando um cluster Spark 2.0 estão em cadernos separados e o caderno de classificação utiliza um conjunto de dados diferente:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar a exploração de dados, modelação e pontuação em clusters Spark 2.0 utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este caderno pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para a Spark 2.0. Para um caderno mais detalhado analisa os dados do Táxi de NYC, consulte o próximo caderno nesta lista. Consulte as notas que seguem esta lista que compara estes cadernos. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando o conhecido conjunto de dados de partida da Companhia Aérea On-time de 2011 e 2012. Integrámos o conjunto de dados da companhia aérea com os dados meteorológicos do aeroporto (por exemplo, velocidade do vento, temperatura, altitude, etc.) antes da modelação, para que estas características meteorológicas possam ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados da companhia aérea foi adicionado aos cadernos Spark 2.0 para ilustrar melhor o uso de algoritmos de classificação. Consulte os seguintes links para obter informações sobre o conjunto de dados de partida a tempo da companhia aérea e o conjunto de dados meteorológicos:
> 
> - Dados de partida a tempo das companhias aéreas:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dados meteorológicos do aeroporto:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Os cadernos Spark 2.0 nos conjuntos de dados de atraso de táxi e de voo da companhia aérea de NYC podem demorar 10 minutos ou mais a funcionar (dependendo do tamanho do seu cluster HDI). O primeiro caderno na lista acima mostra muitos aspetos da exploração de dados, visualização e formação de modelos ML num caderno que leva menos tempo a ser executado com conjunto de dados de NYC amostrada, em que os ficheiros de táxi e tarifa foram pré-aderidos: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Este caderno demora muito menos tempo a terminar (2-3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para a Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> As descrições abaixo estão relacionadas com a utilização da Faísca 1.6. Para versões Spark 2.0, utilize os cadernos descritos e ligados acima. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuração: locais de armazenamento, bibliotecas e o contexto de faísca predefinida
Spark é capaz de ler e escrever para Azure Storage Blob (também conhecido como WASB). Assim, qualquer um dos seus dados existentes lá armazenados pode ser processado usando a Spark e os resultados armazenados novamente no WASB.

Para guardar modelos ou ficheiros no WASB, o caminho tem de ser especificado corretamente. O recipiente predefinido ligado ao cluster Spark pode ser referenciado utilizando um caminho que comece com: "wasb:///". Outros locais são referenciados por "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Delineie caminhos de diretório para locais de armazenamento em WASB
A amostra de código seguinte especifica a localização dos dados a ler e o caminho para o diretório de armazenamento do modelo para o qual a saída do modelo é guardada:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotecas de importação
A criação requer também a importação de bibliotecas necessárias. Definir o contexto de faísca e importar bibliotecas necessárias com o seguinte código:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de faísca predefinida e magias PySpark
Os núcleos PySpark que são fornecidos com cadernos Jupyter têm um contexto predefinido. Por isso, não precisa de definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com a aplicação que está a desenvolver. Estes contextos estão disponíveis por defeito. Estes contextos são:

* sc - para Spark 
* sqlContext - para a Colmeia

O kernel PySpark fornece algumas "magias" predefinidas, que são comandos especiais que se pode chamar com %%. Há dois comandos que são usados nestas amostras de código.

* **%%local** Especifica que o código nas linhas subsequentes deve ser executado localmente. O código deve ser válido código Python.
* **%%sql -o \<nome variável>** Executa uma consulta de Colmeia contra o sqlContext. Se o parâmetro -o for passado, o resultado da consulta é persistente no contexto %%local python como um Pandas DataFrame.

Para obter mais informações sobre os núcleos de cadernos Jupyter e as "magias" predefinidas, consulte [kernels disponíveis para os cadernos Jupyter com clusters HDInsight Spark Linux no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados por bolha pública
O primeiro passo no processo de ciência de dados é ingerir os dados a analisar a partir de fontes onde reside no seu ambiente de exploração e modelação de dados. O ambiente é Spark neste passeio. Esta secção contém o código para completar uma série de tarefas:

* ingerir a amostra de dados a ser modelado
* ler no conjunto de dados de entrada (armazenado como um ficheiro .tsv)
* formato e limpar os dados
* criar e cache objetos (RDDs ou data-frames) na memória
* registem-na como uma tabela temporária no contexto SQL.

Aqui está o código para a ingestão de dados.

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

Tempo de execução acima da célula: 51,72 segundos

## <a name="data-exploration--visualization"></a>Visualização & de exploração de dados
Uma vez que os dados tenham sido trazidos para a Spark, o próximo passo no processo de ciência de dados é obter uma compreensão mais profunda dos dados através da exploração e visualização. Nesta secção, examinamos os dados do táxi usando consultas SQL e traçamos as variáveis-alvo e as funcionalidades prospetivas para inspeção visual. Especificamente, traçamos a frequência das contagens de passageiros em viagens de táxi, a frequência dos valores das gorjetas, e como as gorjetas variam em função do valor e tipo de pagamento.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Defina um histograma de frequências de contagem de passageiros na amostra de viagens de táxi
Este código e os subsequentes snippets usam magia SQL para consultar a amostra e a magia local para traçar os dados.

* **Magia SQL`%%sql`()** O kernel PySpark HDInsight suporta consultas fáceis de linha HiveQL contra o sqlContext. O argumento (-o VARIABLE_NAME) persiste a saída da consulta SQL como um Pandas DataFrame no servidor Jupyter. Esta definição disponibiliza a saída no modo local.
* A ** `%%local` magia** é usada para executar código localmente no servidor Jupyter, que é o headnode do cluster HDInsight. Normalmente, usa-se `%%local` magia `%%sql` em conjunto com a magia com o parâmetro. O parâmetro -o persistiria a produção da consulta SQL localmente e, em seguida, %%magia local desencadearia o próximo conjunto de código snippet para correr localmente contra a saída das consultas SQL que é persistida localmente

A saída é visualizada automaticamente depois de executar o código.

Esta consulta recupera as viagens por contagem de passageiros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Este código cria um quadro de dados local a partir da saída de consulta e traça os dados. A `%%local` magia cria um quadro `sqlResults`de dados local, que pode ser usado para conspirar com matplotlib. 

> [!NOTE]
> Esta magia PySpark é usada várias vezes neste passeio. Se a quantidade de dados for grande, deve provar para criar um quadro de dados que possa caber na memória local.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Aqui está o código para traçar as viagens por contagens de passageiros

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

Pode selecionar entre vários tipos diferentes de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões de menu **Tipo** no caderno. O lote do Bar é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Defina um histograma de quantidades de gorjeta e como o valor da gorjeta varia em termos de contagem de passageiros e valores de tarifa.
Utilize uma consulta SQL para recolher dados.

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


Esta célula de código usa a consulta SQL para criar três parcelas dos dados.

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

![Distribuição de valor de gorjeta](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Valor da gorjeta por contagem de passageiros](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Valor da gorjeta por valor da tarifa](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Engenharia de recursos, transformação e preparação de dados para modelação
Esta secção descreve e fornece o código para os procedimentos utilizados para preparar dados para utilização na modelação ML. Mostra como fazer as seguintes tarefas:

* Criar uma nova funcionalidade ligando horas em baldes de tempo de tráfego
* Características categóricas de indexação e codificação
* Criar objetos de ponto seletivos para entrada nas funções ML
* Criar uma subamostragem aleatória dos dados e dividi-lo em conjuntos de treino e teste
* Dimensionamento de funcionalidades
* Objetos cache na memória

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar uma nova funcionalidade ligando horas em baldes de tempo de tráfego
Este código mostra como criar uma nova funcionalidade, ligando horas aos baldes do tempo de tráfego e, em seguida, como colocar o quadro de dados resultante na memória. Quando os Conjuntos de Dados Distribuídos Resilientes (RDDs) e os data-frames são usados repetidamente, o cache leva a tempos de execução melhorados. Assim, cache RDDs e data-frames em várias fases da passagem. 

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

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexe e codificar características categóricas para entrada em funções de modelação
Esta secção mostra como indexar ou codificar características categóricas para a entrada nas funções de modelação. As funções de modelação e previsão do MLlib exigem que as funcionalidades com dados de entrada categóricos sejam indexadas ou codificadas antes da utilização. Dependendo do modelo, é necessário indexá-los ou codificar de diferentes formas:  

* A **modelação à base de árvores** requer que as categorias sejam codificadas como valores numéricos (por exemplo, uma característica com três categorias pode ser codificada com 0, 1, 2). Este algoritmo é fornecido pela função [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) da MLlib. Esta função codifica uma coluna de etiquetas de cadeia sabotada a uma coluna de índices de etiquetas que são encomendadas por frequências de etiquetas. Embora indexados com valores numéricos para entrada e tratamento de dados, os algoritmos à base de árvores podem ser especificados para tratá-los adequadamente como categorias. 
* **Os modelos de regressão logística e linear** requerem codificação única, onde, por exemplo, uma característica com três categorias pode ser expandida em três colunas de características, cada uma contendo 0 ou 1 dependendo da categoria de observação. O MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para fazer codificação única. Este codificador mapeia uma coluna de índices de etiquetas para uma coluna de vetores binários, com, no máximo, um único valor. Esta codificação permite que algoritmos que esperam características numéricas valorizadas, como a regressão logística, sejam aplicados a características categóricas.

Aqui está o código para indexar e codificar características categóricas:

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

Tempo de execução acima da célula: 1,28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos de ponto seletivos para entrada nas funções ML
Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto seletiva e codifica-lo para que possa ser usado para treinar e testar a regressão logística mLlib e outros modelos de classificação. Os objetos de ponto supor são conjuntos de dados distribuídos resilientes (RDD) formatados de uma forma que é necessária como dados de entrada pela maioria dos algoritmos ML em MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou escasso, associado a um rótulo/resposta.  

Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados [de ponto seletiva e](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) codifica-lo para que possa ser usado para treinar e testar a regressão logística mLlib e outros modelos de classificação. Os objetos de ponto marcados são conjuntos de dados distribuídos resilientes (RDD) constituídos por um rótulo (variável alvo/resposta) e vetor de características. Este formato é necessário como entrada por muitos algoritmos ml em MLlib.

Aqui está o código para indexar e codificar as funcionalidades de texto para a classificação binária.

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


Aqui está o código para codificar e indexar características de texto categóricos para análise linear de regressão.

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


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Criar uma subamostragem aleatória dos dados e dividi-lo em conjuntos de treino e teste
Este código cria uma amostra geminada aleatória dos dados (25% é utilizado aqui). Embora não seja necessário para este exemplo devido ao tamanho do conjunto de dados, demonstramos como pode provar aqui para que saiba usá-lo para o seu próprio problema quando necessário. Quando as amostras são grandes, a amostragem pode economizar tempo significativo durante os modelos de treino. Em seguida, dividimos a amostra numa parte de formação (75% aqui) e numa parte de teste (25% aqui) para usar na classificação e na modelação de regressão.

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

Tempo de execução acima da célula: 0,24 segundos

### <a name="feature-scaling"></a>Dimensionamento de funcionalidades
A escala de funcionalidades, também conhecida como normalização de dados, assegura que as características com valores amplamente desembolsados não são dadas excessivamente pesadas na função objetiva. O código para a escala de funcionalidades utiliza o [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para escalar as funcionalidades para a variação da unidade. É fornecido pela MLlib para uso em regressão linear com Stochastic Gradient Descent (SGD), um algoritmo popular para treinar uma vasta gama de outros modelos de aprendizagem automática, tais como regressões regularizadas ou máquinas vetoriais de suporte (SVM).

> [!NOTE]
> Descobrimos que o algoritmo LinearRegressionWithSGD é sensível à escala de recursos.
> 
> 

Aqui está o código para escalar variáveis para uso com o algoritmo SGD linear regularizado.

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

Tempo de execução acima da célula: 13,17 segundos

### <a name="cache-objects-in-memory"></a>Objetos cache na memória
O tempo de formação e teste de algoritmos ML pode ser reduzido, apertando os objetos de moldura de dados de entrada utilizados para classificação, regressão e características dimensionadas.

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

Tempo de execução acima da célula: 0,15 segundos

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever se uma gorjeta é ou não paga com modelos de classificação binária
Esta secção mostra como utilizar três modelos para a tarefa de classificação binária de prever se uma gorjeta é ou não paga para uma viagem de táxi. Os modelos apresentados são:

* Regressão logística regularizada 
* Modelo de floresta aleatória
* Árvores impulsionadoras de gradiente

Cada secção de código de construção de modelos é dividida em etapas: 

1. Dados de formação de **modelos** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em bolha para consumo futuro

### <a name="classification-using-logistic-regression"></a>Classificação usando regressão logística
O código nesta secção mostra como treinar, avaliar e salvar um modelo de regressão logística com a [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se uma gorjeta é ou não paga por uma viagem na viagem de táxi de NYC e dataset de tarifas.

**Treine o modelo de regressão logística usando CV e varredura de hiperparâmetro**

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

Coeficientes: [0.0082065285375, -0.022367576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.0016589781503, 0.0675394837328, -0.111823131101, -0.32460912762, -0,204549780032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.204549780032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454978032, -0.20454 -1.36499216354, 0.591088507921, -0.66426341392, -1.00439726852, 3.46567827545, -3.5102585172, -0.047134112232, -0.043521833294, 0.00024375810385, 0.054518719222]

Interceção: -0.011121648893

Tempo de execução acima da célula: 14,43 segundos

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

Área sob PR = 0,985297691373

Área sob ROC = 0,983714670256

Estatísticas sumárias

Precisão = 0,984304060189

Recordação = 0.984304060189

Pontuação F1 = 0,984304060189

Tempo de execução acima da célula: 57,61 segundos

**Traçar a curva roc.**

A *previsão AndLabelsDF* está registada como uma tabela, *tmp_results,* na célula anterior. *tmp_results* podem ser usados para fazer consultas e resultados de saída no quadro de dados sqlResults para conspiração. Aqui está o código.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Aqui está o código para fazer previsões e traçar a curva ROC.

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

![Regressão logística roc curva.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação florestal aleatória
O código nesta secção mostra como treinar, avaliar e salvar um modelo florestal aleatório que prevê se uma gorjeta é ou não paga por uma viagem na viagem de táxi de NYC e dataset de tarifas.

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

Área sob ROC = 0,985297691373

Tempo de execução acima da célula: 31,09 segundos

### <a name="gradient-boosting-trees-classification"></a>Gradiente impulsionando a classificação das árvores
O código nesta secção mostra como treinar, avaliar e salvar um modelo de subida de gradiente de árvores que prevê se uma gorjeta é ou não paga por uma viagem na viagem de táxi de NYC e conjunto de dados de tarifas.

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

Área sob ROC = 0,985297691373

Tempo de execução acima da célula: 19,76 segundos

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Preveja valores de gorjeta para viagens de táxi com modelos de regressão
Esta secção mostra como se utilizam três modelos para a tarefa de regressão de prever o valor da gorjeta paga por uma viagem de táxi com base em outras características da ponta. Os modelos apresentados são:

* Regressão linear regularizada
* Floresta aleatória
* Árvores impulsionadoras de gradiente

Estes modelos foram descritos na introdução. Cada secção de código de construção de modelos é dividida em etapas: 

1. Dados de formação de **modelos** com um conjunto de parâmetros
2. **Avaliação do modelo** num conjunto de dados de teste com métricas
3. **Modelo de poupança** em bolha para consumo futuro

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD
O código nesta secção mostra como usar características dimensionadas para treinar uma regressão linear que utiliza a descendência de gradiente estocástico (SGD) para otimização, e como pontuar, avaliar e salvar o modelo em Armazenamento De Blob Azure (WASB).

> [!TIP]
> Na nossa experiência, pode haver problemas com a convergência dos modelos LinearRegressionWithSGD, e os parâmetros precisam de ser alterados/otimizados cuidadosamente para obter um modelo válido. A escala de variáveis ajuda significativamente na convergência. 
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

Coeficientes: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.35963405999, 0.00928692253981, -0.00098718181489428, -0.088830617845, 0.0569376211553, -0.088830617845, 0.0569376211553, 0.1155195111, 0.149250164995, -0.0099021159703, -0.00637410344522, 0.54508356179, -0.536756072402, 0.0105762393099, -0.01301177055, 0.012930477772, -0.00171065945959]

Interceção: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0,608017146081

Tempo de execução acima da célula: 58,42 segundos

### <a name="random-forest-regression"></a>Regressão da Floresta Aleatória
O código nesta secção mostra como treinar, avaliar e salvar uma regressão florestal aleatória que prevê a quantidade de gorjeta para os dados da viagem de táxi de NYC.

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

Tempo de execução acima da célula: 49,21 segundos

### <a name="gradient-boosting-trees-regression"></a>Gradiente impulsionando a regressão das árvores
O código nesta secção mostra como treinar, avaliar e salvar um modelo de subida de gradiente de árvores que prevê a quantidade de gorjeta para os dados da viagem de táxi de NYC.

**Treinar e avaliar**

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

R-sqr = 0,753835096681

Tempo de execução acima da célula: 34,52 segundos

**Enredo**

*tmp_results* está registado como uma mesa da Colmeia na célula anterior. Os resultados da tabela são a saída para o quadro de dados *sqlResults* para a conspiração. Aqui está o código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Aqui está o código para traçar os dados usando o servidor Jupyter.

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

![Montantes reais vs-previsão de gorjeta](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Limpar objetos da memória
Utilize `unpersist()` para eliminar objetos em cache na memória.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Locais de armazenamento recorde dos modelos para consumo e pontuação
Para consumir e marcar um conjunto de dados independente descrito no [Tópico de Modelos](spark-model-consumption.md) de Aprendizagem automática construídos pela Spark, é necessário copiar e colar estes nomes de ficheiros contendo os modelos guardados criados aqui no caderno Do Consumo Jupyter. Aqui está o código para imprimir os caminhos para os ficheiros de modelos que você precisa lá.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SAÍDA**

logísticaRegFileLoc = modeloDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modeloDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Passos seguintes?
Agora que criou modelos de regressão e classificação com o Spark MlLib, está pronto para aprender a pontuar e avaliar estes modelos. O avançado caderno de exploração de dados e modelação mergulha mais profundamente, incluindo validação cruzada, varredura de hiperparâmetros e avaliação de modelos. 

**Consumo de modelo:** Para aprender a pontuar e avaliar os modelos de classificação e regressão criados neste tópico, consulte [o Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

**Validação cruzada e varredura de hiperparâmetros**: Ver [advanced a exploração e modelação de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros

