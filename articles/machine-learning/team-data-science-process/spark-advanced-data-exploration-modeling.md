---
title: Exploração de dados avançada e modelação com o Spark - Team Data Science Process
description: Utilize o Spark do HDInsight para fazer a exploração de dados e formar modelos de classificação e regressão binários com otimização de validação cruzada e de hiper-parâmetros.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 02/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6678a37b534db5603aed245bdc8edffbf04b7f51
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904766"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Exploração e modelação avançada de dados com o Spark

Estas instruções utilizam o Spark do HDInsight para fazer a exploração de dados e preparar os modelos de regressão com a validação cruzada e de classificação binária e a otimização de hiper-parâmetros de uma amostra do NYC táxis viagem e se comportarão de conjunto de dados de 2013. Ele explica-lhe os passos para o [processo de ciência de dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)ponto-a- ponto, utilizar um cluster do HDInsight Spark para blobs de processamento e do Azure para armazenar os dados e os modelos. O processo explora e visualiza dados trazidos de um Blob de armazenamento do Azure e, em seguida, prepara os dados para criar modelos preditivos. Python tem sido utilizado para a solução de código e para mostrar os gráficos relevantes. Estes modelos são de compilação usando o Kit de ferramentas do Spark MLlib para efetuar tarefas de modelagem de classificação binária e regressão. 

* O **classificação binária** tarefa é prever se ou não uma dica é pago para a viagem. 
* O **regressão** tarefa é prever a quantidade de sugestão com base em outros recursos de dica. 

Os passos de modelação também contêm código que mostra como treinar, avaliar e guardar cada tipo de modelo. O tópico abrange alguns dos mesmo zero como o [exploração de dados e modelação com o Spark](spark-data-exploration-modeling.md) tópico. Mas ele é mais "avançado" em que ele também usa a validação cruzada com hiper-parâmetros varrimento a criar modelos de classificação e regressão ideal precisos. 

**Validação cruzada (CV)** é uma técnica que avalie o quão bem um modelo de formação num conjunto conhecido de dados generaliza para prever as funcionalidades dos conjuntos de dados no qual ele não foi preparado.  Uma implementação comum usada aqui é dividir um conjunto de dados em subconjuntos K e, em seguida, preparar o modelo de uma forma round robin, em apenas uma dos subconjuntos. A capacidade do modelo de previsão com precisão quando testado em relação ao conjunto de dados independente neste subconjuntos de validação não é utilizado para preparar o modelo é avaliada.

**Otimização de hiper-parâmetros** é o problema de escolher um conjunto de hiperparâmetros para um algoritmo de aprendizagem, geralmente com o objetivo de otimizar uma medida de desempenho do algoritmo num conjunto de dados independente. **Hiperparâmetros** são valores que tem de ser especificados fora o procedimento de treinamento de modelo. Suposições sobre estes valores podem afetar a flexibilidade e a precisão dos modelos. Árvores de decisões têm hiperparâmetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. Máquinas de Vetores de suporte (SVMs) requer que um termo de penalidade misclassification. 

Uma forma comum de executar a otimização de hiper-parâmetros usada aqui é uma pesquisa de grade, ou uma **varrimentos**. Isso consiste em efetuar uma pesquisa exaustiva os valores de um determinado subconjunto de espaço de hiper-parâmetros para um algoritmo de aprendizagem. Validação cruzada pode fornecer uma métrica de desempenho para classificar os resultados ideais, produzidos pelo algoritmo de pesquisa de grade. CV utilizado com problemas de limite de ajuda, como overfitting um modelo de dados de treinamento para que o modelo mantém a capacidade para aplicar ao conjunto geral de dados a partir da qual os dados de treinamento foi extraídos de varrimento de hiper-parâmetros.

Os modelos que usamos incluem regressão logística e linear, florestas aleatórias e árvores de elevada gradação:

* [Regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo de regressão linear que utiliza um método stochastic Gradient gradiente descendente (SGD) e para Otimização e a funcionalidade de dimensionamento para prever as quantidades de sugestão pago. 
* [Regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou regressão "logit", é um modelo de regressão que pode ser utilizado quando a variável dependente é categórica para fazer a classificação de dados. LBFGS é um algoritmo de otimização de quasi-Newton que aproxima-se o algoritmo de Broyden – Fletcher – Goldfarb – Shanno (BFGS) com uma quantidade limitada de memória do computador e que é amplamente usado no machine learning.
* [Florestas aleatórias](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são árvores de árvores de decisões.  Elas combinam muitos árvores de decisão para reduzir o risco de overfitting. Florestas aleatórias são usadas para classificação e regressão e podem lidar com recursos categóricos e podem ser estendidas para a definição de classificação multiclasses. Eles não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Florestas aleatórias são uma do mais bem-sucedidas modelos de machine learning para classificação e regressão.
* [Gradiente aumentado árvores](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são árvores de árvores de decisões. GBTs treinar árvores de decisão iterativamente para minimizar a uma função de perda. GBTs são utilizados para classificação e regressão e pode lidar com recursos categóricos, não requerem o dimensionamento do recurso e podem capturar não linearities e interações de recursos. Eles também podem ser usados numa configuração de classificação de várias classes.

Modelagem de exemplos que utilizam o CV e hiper-parâmetros paramétrico são apresentadas para o problema de classificação binária. Os exemplos mais simples (sem o parâmetro varra) são apresentados no tópico principal para tarefas de regressão. Mas, apêndice, também é apresentada com o net elástico para regressão linear e CV com o uso de abertura de parâmetro para regressão de floresta aleatório de validação. O **elástico net** é um método de regressão regularized para que se ajusta a modelos de regressão linear que linearmente combina as métricas de L1 e L2 como punições da [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) e [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization)métodos.   

> [!NOTE]
> Embora o toolkit de Spark MLlib foi projetado para trabalhar em grandes conjuntos de dados, um exemplo relativamente pequeno (Mb de aproximadamente 30 com linhas de 170K, cerca de 0,1% do conjunto de dados original NYC) é utilizado aqui para sua comodidade. O exercício fornecido aqui é executado com eficiência (em cerca de 10 minutos) num cluster do HDInsight com 2 nós de trabalho. O mesmo código, com pequenas modificações, pode ser utilizado para processar conjuntos de dados maiores, com as modificações apropriadas para a colocação em cache os dados na memória e alterar o tamanho do cluster.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>Programa de configuração: Os clusters do Spark e blocos de notas
Passos de configuração e de código são fornecidos esta explicação passo a passo para utilizar um HDInsight Spark 1.6. Mas blocos de notas do Jupyter são fornecidos para os clusters do HDInsight Spark 1.6 e Spark 2.0. Uma descrição dos blocos de notas e links para eles são fornecidos na [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e o gerenciamento de cluster podem ser ligeiramente diferentes do que é mostrado aqui. Para sua comodidade, aqui estão os links para os blocos de notas do Jupyter para Spark 1.6 e 2.0 para ser executado no kernel do pyspark, do servidor de notas do Jupyter:

### <a name="spark-16-notebooks"></a>Blocos de notas do Spark 1.6

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Inclui tópicos no bloco de notas #1 e desenvolvimento de modelos usando a otimização de hiper-parâmetros e a validação cruzada.

### <a name="spark-20-notebooks"></a>Blocos de notas do Spark 2.0

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar uma exploração de dados, modelação e a classificação em clusters do Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**SAÍDA**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Bibliotecas de importação
Importe bibliotecas necessárias com o código a seguir:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de Spark predefinido e a magia do PySpark
PySpark kernels que são fornecidos com blocos de notas do Jupyter têm um contexto predefinido. Portanto, não é necessário definir o Spark ou do Hive contextos explicitamente antes de começar a trabalhar com a aplicação estiver a desenvolver. Desses contextos são disponíveis para si por predefinição. Desses contextos são:

* SC - para o Spark 
* kontext sqlContext - para o Hive

O kernel do PySpark fornece alguns predefinidas "magia", que são comandos especiais que pode chamar com % %. Existem dois desses comandos que são utilizados nestas amostras de código.

* **% % local** Especifica se o código nas linhas subsequentes está a ser executado localmente. Código tem de ser um código de Python válido.
* **% % sql -o <variable name>**  executa uma consulta do Hive contra o kontext sqlContext. Se o parâmetro -o passado, o resultado da consulta é mantido no % % contexto Python local como um Pandas DataFrame.

Para obter mais informações sobre os kernels de blocos de notas do Jupyter e predefinidos "magics" que fornecem, consulte [Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux clusters no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados do blob público:
A primeira etapa no processo de ciência de dados é para ingestão de dados para ser analisado a partir de origens em que reside em sua exploração de dados e o ambiente de modelação. Este ambiente é o Spark nestas instruções. Esta secção contém o código para concluir uma série de tarefas:

* a amostra de dados seja modelada de ingestão
* o conjunto de dados de entrada (armazenado como um ficheiro. tsv) de leitura
* Formatar e limpar os dados
* criar e armazenar em cache objetos (RDDs ou quadros de dados) na memória
* Registe-o como uma tabela de temp no contexto de SQL.

Aqui está o código para ingestão de dados.

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


**SAÍDA**

Tempo decorrido para executar acima célula: 276.62 segundos

## <a name="data-exploration--visualization"></a>Exploração de dados e visualização
Depois dos dados tem sido colocados em Spark, a próxima etapa no processo de ciência de dados é obter uma compreensão mais aprofundada dos dados por meio de exploração e visualização. Nesta secção, vamos examinar os dados de táxis através de consultas SQL e desenhar as variáveis de destino e os recursos potenciais para inspeção visual. Especificamente, vamos desenhar a frequência de contagens de passageiros em viagens de táxis, a frequência de quantidades de sugestão e como dicas variam consoante o valor do pagamento e o tipo.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Um histograma da frequências de contagem de passageiros no exemplo de viagens de táxis de desenho
Este código e trechos de código subsequentes utilizam "SQL Magic" para consultar o exemplo e mágica local para representar os dados.

* **"SQL Magic" (`%%sql`)** kernel do PySpark do HDInsight suporta consultas de HiveQL de fácil inline contra o kontext sqlContext. O (-s VARIABLE_NAME) argumento persistir o resultado da consulta SQL como um DataFrame Pandas no servidor do Jupyter. Isso significa que está disponível no modo de local.
* O  **`%%local` magia** é utilizada para executar código localmente no servidor do Jupyter, o que é o nó principal do HDInsight cluster. Normalmente, utiliza `%%local` mágico após o `%%sql -o` mágica é utilizada para executar uma consulta. O parâmetro -o persistir o resultado da consulta SQL localmente. O `%%local` mágica aciona o próximo conjunto de trechos de código para executar localmente a saída das consultas SQL que tiver sido persistida localmente. O resultado é visualizado automaticamente depois de executar o código.

Esta consulta obtém as viagens por contagem de passageiros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Este código cria um quadro de dados local a partir da saída da consulta e desenha os dados. O `%%local` mágica cria um quadro de dados local, `sqlResults`, que pode ser utilizado para desenhar com matplotlib. 

> [!NOTE]
> Essa mágica PySpark é utilizada várias vezes nestas instruções. Se a quantidade de dados é grande, deve de exemplo para criar um quadro de dados que cabem na memória local.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Aqui está o código para desenhar as viagens por contagens de passageiros

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

**SAÍDA**

![Frequência de viagens por contagem de passageiros](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Pode selecionar entre os vários tipos diferentes de visualizações (tabela, gráfico de pizza, linha, área ou barra) ao utilizar o **tipo** botões de menu no bloco de notas. O gráfico de barras é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Desenhe um histograma da tip quantidades e como Gorjeta varia por quantidades de contagem e Europeia passageiro.
Utilizar uma consulta SQL para dados de exemplo....

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


Essa célula de código utiliza a consulta SQL para criar três gráficos os dados.

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


**SAÍDA:** 

![Distribuição de quantidade de sugestão](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Gorjeta por contagem de passageiros](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Sugestão quantidade pelo Europeia quantidade](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparação de engenharia, transformação e dados para a Modelagem de recursos
Esta secção descreve e fornece o código para obter os procedimentos utilizados para preparar dados para utilização na modelagem de ML. Ele mostra como realizar as seguintes tarefas:

* Criar um novo recurso ao particionar horas em contentores de tempo de tráfego
* Indexar e no armazenamento de frequente codificar funcionalidades categóricas
* Criar objetos com nome de ponto de entrada para as funções de ML
* Criar uma amostragem aleatória secundárias dos dados e dividi-lo em preparação e teste conjuntos
* Dimensionamento de recursos
* Objetos de cache na memória

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Criar um novo recurso ao particionar os tempos de tráfego em contentores
Este código mostra como criar um novo recurso ao particionar os tempos de tráfego em contentores e, em seguida, como colocar em cache o quadro de dados resultante na memória. Colocação em cache leva a melhor tempo de execução, onde Datasets de distribuídos resilientes (RDDs) e quadros de dados são usados repetidamente. Então, vamos colocar em cache RDDs e quadros de dados em vários estágios nestas instruções.

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

**SAÍDA**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indexar e um-hot codificar funcionalidades categóricas
Esta secção mostra como índice ou codificar categóricas funcionalidades de entrada para as funções de modelagem. A Modelagem e prever a funções de MLlib exigem que os recursos com dados de entrada categóricos ser indexados ou codificados antes de utilizar. 

Dependendo do modelo, terá de índice ou codificá-los de formas diferentes. Por exemplo, modelos de regressão Linear e de Logistic exigem frequente de uma codificação, onde, por exemplo, uma funcionalidade com três categorias pode ser expandida para três colunas de funcionalidades, com cada que contêm 0 ou 1 consoante a categoria de uma observação. Fornece MLlib [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) função para fazer uma-hot codificação. Este codificador mapeia uma coluna de índices de etiqueta a uma coluna de vetores de binários, no máximo um único um valor. Esta codificação permite que os algoritmos que esperam que os recursos de com valores numéricos, como a regressão logística, possam ser aplicadas aos recursos categóricos.

Aqui está o código para indexar e codificar funcionalidades categóricas:

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


**SAÍDA**

Tempo decorrido para executar acima célula: segundos 3.14

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos com nome de ponto de entrada para as funções de ML
Esta secção contém código que mostra como indexar dados categóricos texto como um tipo de dados do ponto de etiquetadas e como codificá-lo. Isto prepara-o para ser usado para dar formação e teste de regressão logística de MLlib e outros modelos de classificação. Objetos point etiquetadas são formatados de forma que é necessária, como dados de entrada, pela maioria dos algoritmos de ML no MLlib com a conjuntos de dados de distribuídos resilientes (RDD). R [rotulado como ponto de](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, densa ou disperso, associado com uma etiqueta/resposta.

Aqui está o código para indexar e codificar as funcionalidades de texto para classificação binária.

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
Este código cria uma amostragem aleatória de dados (25% é utilizado aqui). Embora não seja necessária para este exemplo devido ao tamanho do conjunto de dados, vamos demonstrar como pode apresentar exemplos aqui os dados. Em seguida, sabe como usá-lo para o seu problema, se necessário. Quando os exemplos são grandes, isso pode economizar muito tempo enquanto os modelos de treinamento. Em seguida vamos dividir o exemplo numa parte de treinamento (75% aqui) e uma parte de teste (25% aqui) para classificação e modelação de regressão.

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

**SAÍDA**

Tempo decorrido para executar acima célula: 0.31 segundos

### <a name="feature-scaling"></a>Dimensionamento de recursos
Dimensionamento de recursos, também conhecido como normalização de dados, forma, assegura que funcionalidades com valores amplamente disbursed são não fornecido excessiva pesar na função Objetiva. O código para a funcionalidade de dimensionamento utiliza a [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para dimensionar os recursos a variância de unidade. Ele é fornecido pelo MLlib para utilização em regressão linear com stochastic Gradient gradiente descendente (SGD). SGD é um algoritmo popular para uma vasta gama de outros modelos de machine learning, como máquinas de vetores de suporte (SVM) ou de regularized regressões de formação.   

> [!TIP]
> Descobrimos que o algoritmo de LinearRegressionWithSGD ser sensível à funcionalidade de dimensionamento.   
> 
> 

Aqui está o código para variáveis de dimensionamento para utilização com o algoritmo SGD regularized linear.

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

**SAÍDA**

Tempo decorrido para executar acima célula: 11.67 segundos

### <a name="cache-objects-in-memory"></a>Objetos de cache na memória
O tempo decorrido para a preparação e teste de algoritmos de ML pode ser reduzido ao colocar em cache o quadro de dados de entrada objetos utilizados para classificação, regressão e, dimensionados de funcionalidades.

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

**SAÍDA** 

Tempo decorrido para executar acima célula: 0.13 segundos

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever se ou não uma dica é pago com modelos de classificação binária
Esta secção mostra como utilizar três modelos para a tarefa de classificação binária de prever se ou não uma dica é pago por uma viagem de táxis. Os modelos apresentados são:

* Regressão logística 
* Floresta aleatória
* Gradação árvores adaptativo

Cada modelo de criação de seção de código é dividido em passos: 

1. **Treinamento de modelo** dados com um conjunto de parâmetros
2. **Avaliação de modelo** num conjunto de dados de teste com a métrica
3. **A guardar modelo** no blob para o consumo futuro

Vamos mostrar como fazer a validação cruzada (CV) com o parâmetro varrimento de duas formas:

1. Usando **genérico** código personalizado que pode ser aplicado a qualquer algoritmo no MLlib e para qualquer parâmetro define num algoritmo. 
2. Utilizar o **pySpark função do pipeline de CrossValidator**. Tenha em atenção que CrossValidator tem algumas limitações para o Spark 1.5.0: 
   
   * Modelos de pipeline não podem ser guardado/persistentes para futuro consumo.
   * Não pode ser utilizado para cada parâmetro num modelo.
   * Não pode ser utilizado para cada algoritmo MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Genérico cruzada validação e varrimento de hiper-parâmetros utilizado com o algoritmo de regressão logística de classificação binária
O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se ou não uma dica é pago por uma viagem no NYC táxis viagem e Europeia conjunto de dados. É preparado o modelo usando cruzada validação (CV) e implementado com código personalizado que pode ser aplicado a qualquer um dos algoritmos de aprendizagem em MLlib de varrimento de hiper-parâmetros.   

> [!NOTE]
> A execução desse código CV personalizado pode demorar vários minutos.
> 
> 

**Preparar o modelo de regressão logística usando CV e varrimento de hiper-parâmetros**

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


**SAÍDA**

Coeficientes: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e - 05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intercepção:-0.0111216486893

Tempo decorrido para executar acima célula: 14.43 segundos

**Avaliar o modelo de classificação binária com métricas padrão**

O código nesta secção mostra como avaliar um modelo de regressão logística contra um teste-conjunto de dados, incluindo um desenho da curva cor MULTICLASSE.

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


**SAÍDA**

Área de PR = 0.985336538462

Área de ROC = 0.983383274312

Estatísticas de resumidas

Precisão = 0.984174341679

Lembre-se = 0.984174341679

F1 Pontuação = 0.984174341679

Tempo decorrido para executar acima célula: 2.67 segundos

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


**SAÍDA**

![Curva cor MULTICLASSE de regressão logística de abordagem genérica](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Manter o modelo num blob para futuro consumo**

O código nesta secção mostra como pode guardar o modelo de regressão logística para consumo.

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


**SAÍDA**

Tempo decorrido para executar acima célula: 34.57 segundos

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Utilizar a função de pipeline do MLlib CrossValidator com o modelo de regressão logística (regressão elástica)
O código nesta secção mostra como treinar, avaliar e guardar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê se ou não uma dica é pago por uma viagem no NYC táxis viagem e Europeia conjunto de dados. É preparado o modelo usando cruzada validação (CV) e implementados com a função do pipeline de MLlib CrossValidator para CV com varrimentos de varrimento de hiper-parâmetros.   

> [!NOTE]
> A execução desse código MLlib CV pode demorar vários minutos.
> 
> 

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

**SAÍDA**

Tempo decorrido para executar acima célula: 107.98 segundos

**Representar a curva cor MULTICLASSE.**

O *predictionAndLabelsDF* está registado como uma tabela *tmp_results*, a célula anterior. *tmp_results* pode ser utilizado para fazer consultas e resultados de saída para o quadro de dados sqlResults para desenhar. Aqui está o código.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Aqui está o código para desenhar a curva cor MULTICLASSE.

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


**SAÍDA**

![Curva cor MULTICLASSE de regressão logística usando CrossValidator do MLlib](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação de floresta aleatório
O código nesta secção mostra como treinar, avaliar e guardar uma regressão de floresta aleatória que prevê se ou não uma dica é pago por uma viagem no NYC táxis viagem e Europeia conjunto de dados.

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


**SAÍDA**

Área de ROC = 0.985336538462

Tempo decorrido para executar acima célula: 26.72 segundos

### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores adaptativo gradação
O código nesta secção mostra como treinar, avaliar e guardar um modelo de árvores de adaptativo gradação que preveja se ou não uma dica é pago por uma viagem em viagem de táxis NYC e se comportarão de conjunto de dados.

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

**SAÍDA**

Área de ROC = 0.985336538462

Tempo decorrido para executar acima célula: 28.13 segundos

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prever Gorjeta com modelos de regressão (sem utilizar CV)
Esta secção mostra como utilizar três modelos para a tarefa de regressão: prever o valor de sugestão pago por uma viagem de táxis com base em outros recursos de dica. Os modelos apresentados são:

* Regressão linear regularized
* Floresta aleatória
* Gradação árvores adaptativo

Esses modelos foram descritos na introdução. Cada modelo de criação de seção de código é dividido em passos: 

1. **Treinamento de modelo** dados com um conjunto de parâmetros
2. **Avaliação de modelo** num conjunto de dados de teste com a métrica
3. **A guardar modelo** no blob para o consumo futuro   

> NOTA DO AZURE: A validação cruzada não é utilizada com os modelos de três regressão nesta secção, uma vez que isso foi mostrado em detalhes para os modelos de regressão logística. Um exemplo que mostra como utilizar o CV com elástico Net para regressão linear é disponibilizado no anexo deste tópico.
> 
> NOTA DO AZURE: Na nossa experiência, podem existir problemas com a convergência dos modelos de LinearRegressionWithSGD e parâmetros têm de ser alterado/otimizada com cuidado para obtenção de um modelo válido. Dimensionamento significativamente de variáveis de ajuda com a convergência. Regressão net elástica, mostrado no apêndice a esse tópico, também pode ser utilizado em vez de LinearRegressionWithSGD.
> 
> 

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD
O código nesta secção mostra como usar recursos dimensionados para preparar uma regressão linear que utiliza descendente gradação stochastic gradient (SGD) para a Otimização e como classificar, avaliar e guardar o modelo no armazenamento de Blobs do Azure (WASB).

> [!TIP]
> Na nossa experiência, podem existir problemas com a convergência dos modelos de LinearRegressionWithSGD e parâmetros têm de ser alterado/otimizada com cuidado para obtenção de um modelo válido. Dimensionamento significativamente de variáveis de ajuda com a convergência.
> 
> 

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

**SAÍDA**

Coeficientes: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,- 0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Interceptar: 0.854507624459

RMSE = 1.23485131376

R sqr = 0.597963951127

Tempo decorrido para executar acima célula: 38.62 segundos

### <a name="random-forest-regression"></a>Regressão de floresta de Random
O código nesta secção mostra como treinar, avaliar e guardar um modelo de floresta aleatória que prevê Gorjeta para os dados de viagens de táxis NYC.   

> [!NOTE]
> A validação cruzada com o parâmetro varrimento utilizando código personalizado é fornecida no apêndice.
> 
> 

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

**SAÍDA**

RMSE = 0.931981967875

R-sqr = 0.733445485802

Tempo decorrido para executar acima célula: 25.98 segundos

### <a name="gradient-boosting-trees-regression"></a>Gradação regressão de árvores adaptativo
O código nesta secção mostra como treinar, avaliar e guardar um modelo de árvores de adaptativo gradação que prevê Gorjeta para os dados de viagens de táxis NYC.

**Dar formação e avaliar**

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


**SAÍDA**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Tempo decorrido para executar acima célula: 20.9 segundos

**Plot**

*tmp_results* está registado como uma tabela do Hive a célula anterior. Resultados da tabela são de saída para o *sqlResults* quadro de dados para representar. Aqui está o código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Aqui está o código para desenhar os dados a utilizar o servidor do Jupyter.

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

![Real-vs-previsto-sugestão-quantidades](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Apêndice: Tarefas de regressão adicionais com validação cruzada com varrimentos
Este anexo contém código que mostra como fazer o CV com o net elástico para regressão linear e como fazer o CV com varrimentos com código personalizado para regressão de floresta aleatório.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Utilizar o Elastic net para regressão linear de validação cruzada
O código nesta secção mostra como a validação com o net elástico para regressão linear de cruzada e como avaliar o modelo de dados de teste.

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


**SAÍDA**

Tempo decorrido para executar acima célula: 161.21 segundos

**Avaliar com a métrica de R SQR**

*tmp_results* está registado como uma tabela do Hive a célula anterior. Resultados da tabela são de saída para o *sqlResults* quadro de dados para representar. Aqui está o código

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Aqui está o código para calcular R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**SAÍDA**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Validação cruzada com varrimentos com código personalizado para regressão de floresta aleatório
O código nesta secção mostra como validação cruzada com varrimentos com código personalizado para regressão de floresta aleatório e como avaliar o modelo de dados de teste.

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


**SAÍDA**

RMSE = 0.906972198262

R-sqr = 0.740751197012

Tempo decorrido para executar acima célula: 69.17 segundos

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Limpar objetos da memória e localizações de modelo de impressão
Utilize `unpersist()` eliminar objetos em cache na memória.

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


**SAÍDA**

PythonRDD [122] em RDD em PythonRDD.scala: 43

* * Caminho de printout para arquivos de modelo devem ser usados no bloco de notas de consumo. * * Para consumir e classificar um conjunto de dados independente, terá de copiar e colar esses nomes de ficheiro no "consumo bloco de notas".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SAÍDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Passos seguintes?
Agora que criou os modelos de regressão e de classificação com o Spark MlLib, está pronto para aprender a pontuação e avaliar esses modelos.

**Consumo de modelo:** Para saber como classificar e avaliar os modelos de classificação e regressão criados neste tópico, veja [pontuação e avaliar modelos de aprendizagem automática criados com o Spark](spark-model-consumption.md).

