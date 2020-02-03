---
title: Ciência de dados com o Scala e Spark no Azure - Team Data Science Process
description: Como utilizar o Scala para tarefas de aprendizado de máquina supervisionados com os Spark dimensionáveis MLlib e Spark ML pacotes num cluster do Azure HDInsight Spark.
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
ms.openlocfilehash: b36a3faab49ee8d51c25aa18879e6f5d1db8c2fb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716760"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Utilizar o Scala e o Spark para Ciência de Dados no Azure
Este artigo mostra-lhe como utilizar o Scala para tarefas de aprendizado de máquina supervisionados com os Spark dimensionáveis MLlib e Spark ML pacotes num cluster do Azure HDInsight Spark. Percorre-o através das tarefas que constituem o [processo data Science](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelação e consumo de modelos. Os modelos no artigo incluem regressão logística e linear, florestas aleatórias e aumentou a gradação árvores (GBTs), além de dois supervisionado tarefas de machine learning:

* Problema de regressão: previsão da quantidade de tip ($) para uma viagem de táxis
* Classificação binária: previsão de sugestão ou nenhuma sugestão (1/0) para uma viagem de táxis

O processo de modelagem requer formação e avaliação num conjunto de dados de teste e métricas de precisão relevantes. Neste artigo, pode saber como armazenar esses modelos no armazenamento de Blobs do Azure e como proceder à sua classificação e avaliar seu desempenho de previsão. Este artigo também abrange os tópicos mais avançados de como otimizar modelos utilizando varrimento de validação cruzada e de hyper-parâmetro. Os dados utilizados são um exemplo do 2013 NYC táxis viagem e Europeia conjunto de dados disponível no GitHub.

[Scala,](https://www.scala-lang.org/)uma linguagem baseada na máquina virtual java, integra conceitos linguísticos orientados a objetos e funcionais. É uma linguagem dimensionável, que é adequado para processamento distribuído na cloud sendo executada em clusters do Spark do Azure.

[Spark](https://spark.apache.org/) é um quadro de processamento paralelo de código aberto que suporta o processamento na memória para aumentar o desempenho de aplicações de análise de big data. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos em cálculos de machine learning e do graph. O pacote [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) fornece um conjunto uniforme de APIs de alto nível construídos em cima de quadros de dados que podem ajudá-lo a criar e afinar gasodutos práticos de aprendizagem automática. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de machine learning escalável da Spark, que traz capacidades de modelação para este ambiente distribuído.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta hospedada pelo Azure de spark de código aberto. Também inclui suporte para blocos de notas do Jupyter Scala no cluster do Spark e pode executar consultas interativas do Spark SQL para transformar, filtrar e visualizar os dados armazenados no armazenamento de Blobs do Azure. Executam os trechos de código Scala neste artigo que fornecem as soluções e mostram os gráficos para visualizar os dados relevantes em blocos de notas do Jupyter instalados nos clusters do Spark. Os passos de modelação nos seguintes tópicos tem código que mostra como treinar, avaliar, guardar e consumir cada tipo de modelo.

Os passos de configuração e o código neste artigo são para o Azure HDInsight 3.4 Spark 1.6. No entanto, o código neste artigo e no [Caderno Scala Jupyter](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) é genérico e deve funcionar em qualquer cluster Spark. Os passos de configuração e gestão de cluster podem ser ligeiramente diferentes do que é mostrado neste artigo, se não estiver a utilizar o Spark do HDInsight.

> [!NOTE]
> Para um tópico que lhe mostre como usar Python em vez de Scala para completar tarefas para um processo de ciência de dados de ponta a ponta, consulte [data science usando Spark on Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Precisa de uma subscrição do Azure. Se ainda não tiver um, [faça um teste gratuito ao Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* Precisa de um cluster Azure HDInsight 3.4 Spark 1.6 para concluir os procedimentos seguintes. Para criar um cluster, consulte as instruções em [Get started: Create Apache Spark on Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Desloque o tipo de cluster e a versão no menu **Select Cluster Type.**

![Configuração de tipo de cluster do HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Para obter uma descrição dos dados e instruções da viagem de táxi de NYC sobre como executar código a partir de um caderno Jupyter no cluster Spark, consulte as secções relevantes em [Visão Geral da Ciência dos Dados utilizando a Spark on Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar o código de Scala a partir de um bloco de notas do Jupyter no cluster do Spark
Pode iniciar um bloco de notas do Jupyter no portal do Azure. Localize o cluster do Spark no seu dashboard e, em seguida, clique nele para inserir a página de gestão para o seu cluster. Em seguida, clique em **Cluster Dashboards**, e, em seguida, clique em **Jupyter Notebook** para abrir o caderno associado ao cluster Spark.

![Dashboard de clusters e blocos de notas do Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Você também pode aceder aos cadernos Jupyter em https://&lt;nome de cluster&gt;.azurehdinsight.net/jupyter. Substitua o nome do *cluster* pelo nome do seu cluster. Terá da palavra-passe para a sua conta de administrador acessar os blocos de notas do Jupyter.

![Vá para blocos de notas do Jupyter com o nome do cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecione **Scala** para ver um diretório que tem alguns exemplos de cadernos pré-embalados que usam a API PySpark. O Modelação e Pontuação de Exploração utilizando o portátil Scala.ipynb que contém as amostras de código para este conjunto de tópicos Spark está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Pode carregar o bloco de notas diretamente a partir do GitHub para o servidor de bloco de notas do Jupyter no cluster do Spark. Na sua página inicial do Jupyter, clique no botão **Upload.** No explorador de ficheiros, colhe o URL gitHub (conteúdo bruto) do caderno Scala e, em seguida, clique em **Open**. O bloco de notas Scala está disponível no seguinte URL:

[Exploração-Modelação e Pontuação-scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Programa de configuração: Contextos do Spark de configuração predefinida e do Hive, magia do Spark e bibliotecas de Spark
### <a name="preset-spark-and-hive-contexts"></a>Configuração predefinida contextos do Spark e do Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Os kernels do Spark que são fornecidos com blocos de notas do Jupyter tem a configuração predefinida contextos. Não precisa de definir explicitamente o Spark ou contextos de ramo de registo antes de começar a trabalhar com a aplicação estiver a desenvolver. Os contextos predefinidos são:

* `sc` para SparkContext
* `sqlContext` para hiveContext

### <a name="spark-magics"></a>Magia de Spark
O kernel spark fornece algumas "magias" predefinidas, que são comandos especiais que se pode chamar com `%%`. Dois desses comandos são utilizados nos exemplos de código a seguir.

* `%%local` especifica que o código nas linhas subsequentes será executado localmente. O código tem de ser um código Scala válido.
* `%%sql -o <variable name>` executa uma consulta da Colmeia contra `sqlContext`. Se o parâmetro `-o` for passado, o resultado da consulta é persistente no contexto `%%local` Scala como um quadro de dados spark.

Para obter mais informações sobre os núcleos dos cadernos Jupyter e as suas "magias" predefinidas a que chama com `%%` (por exemplo, `%%local`), consulte [os Kernels disponíveis para os cadernos Jupyter com clusters HDInsight Spark Linux no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Bibliotecas de importação
Importe o Spark MLlib e outras bibliotecas que precisará utilizando o código a seguir.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Ingestão de dados
A primeira etapa no processo de ciência de dados é ingerir os dados que pretende analisar. Colocar os dados de fontes externas ou sistemas em que reside no seu ambiente de exploração e modelação de dados. Neste artigo, os dados, ingerir são um exemplo de 0,1% associado do ficheiro de viagem e Europeia de táxis (armazenado como um ficheiro. tsv). O ambiente de exploração e modelação de dados é o Spark. Esta secção contém o código para concluir a seguinte série de tarefas:

1. Definir caminhos de diretório para o armazenamento de dados e o modelo.
2. Leia no conjunto de dados de entrada (armazenado como um ficheiro. tsv).
3. Definir um esquema para os dados e limpar os dados.
4. Criar um quadro de dados limpo e cache na memória.
5. Registre-se os dados como uma tabela temporária na kontext SQLContext.
6. Consultar a tabela e importe os resultados para um quadro de dados.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir caminhos de diretório para localizações de armazenamento no armazenamento de Blobs do Azure
Spark pode ler e escrever no armazenamento de Blobs do Azure. Pode utilizar o Spark para processar qualquer um dos seus dados existentes e, em seguida, armazenar os resultados novamente no armazenamento de Blobs.

Para guardar modelos ou ficheiros no armazenamento de BLOBs, terá de especificar corretamente o caminho. Refira-se ao recipiente predefinido ligado ao cluster Spark utilizando um caminho que começa com `wasb:///`. Referenciar outros locais utilizando `wasb://`.

O código de exemplo seguinte especifica a localização de dados de entrada para ser lida e o caminho para o armazenamento de BLOBs que está ligado ao cluster do Spark onde o modelo será guardado.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar dados, criar um RDD e definir um quadro de dados, de acordo com o esquema
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 8 segundos.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Consultar a tabela e importar os resultados num quadro de dados
Em seguida, consultar a tabela para Europeia, passageiros e dados da tip; filtrar os dados danificados e distantes. e imprimir várias linhas.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Saída:**

| fare_amount | passenger_count | tip_amount | colocado para |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Exploração de dados e visualização
Depois de colocar os dados no Spark, a próxima etapa no processo de ciência de dados é obter uma compreensão mais aprofundada dos dados por meio de exploração e visualização. Nesta seção, examinar os dados de táxis utilizando consultas SQL. Em seguida, importe os resultados num quadro de dados para traçar as variáveis-alvo e as funcionalidades prospetivas para a inspeção visual utilizando a funcionalidade de visualização automática Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Utilizar o local e "SQL Magic" para representar dados
Por predefinição, a saída de qualquer trecho de código que executar a partir de um bloco de notas do Jupyter está disponível dentro do contexto da sessão que é mantido em nós de trabalho. Se quiser guardar uma viagem aos nós dos trabalhadores para cada computação, e se todos os dados necessários para a sua computação estiverem disponíveis localmente no nó do servidor Jupyter (que é o nó da cabeça), pode usar a magia `%%local` para executar o corte de código no servidor Jupyter.

* **Magia SQL** (`%%sql`). O kernel de Spark do HDInsight suporta consultas HiveQL inline fácil kontext SQLContext. O argumento (`-o VARIABLE_NAME`) persiste a saída da consulta SQL como uma moldura de dados pandas no servidor Jupyter. Esta definição significa que a saída estará disponível no modo local.
* `%%local` **magia.** A magia `%%local` executa o código localmente no servidor Jupyter, que é o nó de cabeça do cluster HDInsight. Normalmente, usa-se `%%local` magia em conjunto com a magia `%%sql` com o parâmetro `-o`. O parâmetro `-o` persistiria a saída da consulta SQL localmente, e então `%%local` magia desencadearia o próximo conjunto de códigos para correr localmente contra a saída das consultas SQL que persiste localmente.

### <a name="query-the-data-by-using-sql"></a>Consultar os dados com o SQL
Esta consulta obtém as viagens de táxis pela quantidade de Europeia, a contagem de passageiros e Gorjeta.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

No seguinte código, a magia `%%local` cria uma moldura de dados local, sqlResults. Pode usar sqlResults a representar usando matplotlib.

> [!TIP]
> Magic de local é utilizado várias vezes neste artigo. Se o seu conjunto de dados é grande, de exemplo, para criar um quadro de dados que cabem na memória local.
> 
> 

### <a name="plot-the-data"></a>Os dados de desenho
Pode desenhar com o código de Python depois do quadro de dados no contexto local como um quadro de dados Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 O kernel de Spark visualiza automaticamente a saída de consultas SQL (HiveQL) depois de executar o código. Pode escolher entre vários tipos de visualizações:

* Tabela
* Circular
* Linha
* Área
* Barra

Aqui está o código para desenhar os dados:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Saída:**

![Histograma da quantidade de sugestão](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Gorjeta por contagem de passageiros](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Gorjeta pela quantidade de Europeia](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Criar recursos e funcionalidades de transformação e, em seguida, de preparação de dados de entrada para as funções de modelagem
Para funções de modelagem com base na árvore de Spark ML e MLlib, terá de preparar o destino e funcionalidades utilizando uma variedade de técnicas, como discretização, indexação, frequente de uma codificação e vetorização. Aqui estão os procedimentos a seguir nesta secção:

1. Crie uma nova funcionalidade **ligando** horas aos baldes de tempo de tráfego.
2. Aplique **indexação e codificação one-hot** a características categóricas.
3. **Experimente e divida os dados em** frações de treino e teste.
4. **Especifique a variável e características**de formação , e, em seguida, crie treino sem limites indexados ou um quente codificado e teste os conjuntos de dados distribuídos resilientes do ponto de distribuição (RDDs) ou os quadros de dados.
5. **Categorize e vectorize** automaticamente funcionalidades e alvos para usar como inputs para modelos de aprendizagem automática.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar um novo recurso por horas de discretização em registos de tempo de tráfego
Este código mostra como criar um novo recurso por horas de discretização em registos de tempo de tráfego e como armazenar em cache o quadro de dados resultante na memória. Onde os quadros de dados e RDDs são usados repetidamente, colocação em cache o leva a mais tempos de execução. Da mesma forma, armazenar em cache RDDs e quadros de dados em vários estágios nos seguintes procedimentos.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexação e a codificação de frequente de um dos recursos categóricos
A Modelagem e prever as funções de MLlib necessitam de funcionalidades com dados categóricos de entrada a ser indexados ou codificada antes de utilizar. Esta secção mostra como índice ou codificar categóricas funcionalidades de entrada para as funções de modelagem.

Terá de índice ou codificar seus modelos de diversas formas, consoante o modelo. Por exemplo, modelos de regressão logística e linear exigem frequente de uma codificação. Por exemplo, uma funcionalidade com três categorias pode ser expandida para três colunas de funcionalidades. Cada coluna iria conter 0 ou 1 consoante a categoria de uma observação. O MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para codificação de uma única ligação. Este codificador mapeia uma coluna de índices de etiqueta a uma coluna de vetores de binários no máximo um único um valor. Com essa codificação, os algoritmos que esperam recursos com valores numéricos, como a regressão logística, podem ser aplicados às funcionalidades categóricas.

Aqui transformar apenas quatro variáveis para mostrar os exemplos, que são cadeias de caracteres. Também pode indexar outras variáveis, como o dia da semana, representados pelos valores numéricos, como variáveis categóricas.

Para indexar, utilize `StringIndexer()`, e para codificação pontual, utilize funções `OneHotEncoder()` a partir de MLlib. Aqui está o código para indexar e codificar funcionalidades categóricas:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 4 segundos.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exemplo e dividir o conjunto de dados em frações de preparação e teste
Este código cria uma amostragem aleatória dos dados (25%, neste exemplo). Embora não seja necessária para este exemplo devido ao tamanho do conjunto de dados de amostragem, o artigo mostra-lhe como pode apresentar exemplos para que saiba como usá-lo para seus próprios problemas quando necessário. Quando os exemplos são grandes, isso pode economizar um tempo significativo enquanto formar modelos. Em seguida, o exemplo ser dividido numa parte de treinamento (75%, neste exemplo) e uma parte de teste (25%, neste exemplo), para classificação e modelação de regressão.

Adicione um número aleatório (entre 0 e 1) a cada linha (numa coluna de "rand") que pode ser utilizada para selecionar os subconjuntos de validação cruzada durante o treinamento.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 2 segundos.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especificar a variável de treinamento e recursos e, em seguida, criar indexada ou frequente de um codificado preparação e teste rotulado como quadros de dados ou RDDs de ponto de entrada
Esta secção contém código que mostra como indexar dados categóricos texto como um tipo de dados do ponto de etiquetadas e codificá-lo para que possa utilizá-lo a dar formação e teste de regressão logística de MLlib e outros modelos de classificação. Objetos point etiquetadas são RDDs são formatados de forma que é necessária, como dados de entrada, pela maioria dos algoritmos no MLlib de machine learning. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou escasso, associado a um rótulo/resposta.

Nesse código, especifique a variável (dependente) de destino e os recursos para utilizar para preparar modelos. Em seguida, criar indexada ou frequente de um codificado preparação e teste rotulado como quadros de dados ou RDDs de ponto de entrada.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 4 segundos.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorizar e vectorize metas para utilizar como entradas para modelos de machine learning e de recursos automaticamente
Utilize o Spark ML para categorizar o destino e funcionalidades para utilizar nas funções de modelagem com base na árvore. O código for concluído duas tarefas:

* Cria um destino de binário para classificação, atribuindo um valor de 0 ou 1 para cada ponto de dados entre 0 e 1 utilizando um valor de limite de 0,5.
* Categoriza automaticamente os recursos. Se o número de valores numéricos distintos para qualquer funcionalidade for inferior a 32, essa funcionalidade é categorizada.

Aqui está o código para essas duas tarefas.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de classificação binária: prever se uma dica deve ser pago
Nesta secção, vai criar três tipos de modelos de classificação binária para prever se ou não deve ser pago uma dica:

* Um modelo de **regressão logística** utilizando a função Spark ML `LogisticRegression()`
* Um **modelo de classificação florestal aleatório** utilizando a função Spark ML `RandomForestClassifier()`
* Um **gradiente impulsionando** o modelo de classificação de árvores usando a função MLlib `GradientBoostedTrees()`

### <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística
Em seguida, crie um modelo de regressão logística utilizando a função Spark ML `LogisticRegression()`. Criar o modelo de criação de código numa série de passos:

1. **Treine os** dados do modelo com um conjunto de parâmetros.
2. **Avalie o modelo** num conjunto de dados de teste com métricas.
3. **Guarde o modelo** no armazenamento blob para consumo futuro.
4. **Marque o modelo** contra os dados do teste.
5. **Defina os resultados** com curvas características de funcionamento do recetor (ROC).

Aqui está o código para estes procedimentos:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Carregar, classificação e salvar os resultados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Saída:**

Cor MULTICLASSE em dados de teste = 0.9827381497557599

Utilizar o Python em quadros de dados do locais Pandas para desenhar a curva cor MULTICLASSE.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Saída:**

![Sugestão ou sem curva cor MULTICLASSE de sugestão](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Criar um modelo de classificação aleatória de floresta
Em seguida, crie um modelo de classificação florestal aleatório utilizando a função de `RandomForestClassifier()` Spark ML e, em seguida, avalie o modelo em dados de teste.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Saída:**

Cor MULTICLASSE em dados de teste = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Criar um modelo de classificação GBT
Em seguida, crie um modelo de classificação GBT utilizando a função `GradientBoostedTrees()` da MLlib e, em seguida, avalie o modelo em dados de teste.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Saída:**

Área em curva cor MULTICLASSE: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modelo de regressão: prever a quantidade de sugestão
Nesta secção, vai criar dois tipos de modelos de regressão para prever a quantidade de dica:

* Um modelo de **regressão linear regularizado** utilizando a função Spark ML `LinearRegression()`. Irá guardar o modelo e avaliar o modelo em dados de teste.
* Um modelo de regressão de **árvores impulsionadorde gradiente** utilizando a função de `GBTRegressor()` Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Criar um modelo de regressão linear regularized
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 13 segundos.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Saída:**

R-sqr em dados de teste = 0.5960320470835743

Em seguida, consultar os resultados do teste como um quadro de dados e utilize AutoVizWidget e matplotlib para visualizá-la.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

O código cria um quadro de dados local a partir da saída da consulta e desenha os dados. A magia `%%local` cria uma moldura de dados local, `sqlResults`, que pode usar para traçar com matplotlib.

> [!NOTE]
> Essa mágica de Spark é utilizada várias vezes neste artigo. Se a quantidade de dados é grande, deve de exemplo para criar um quadro de dados que cabem na memória local.
> 
> 

Crie gráficos com Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Saída:**

![Sugestão quantidade: real vs. prevista](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Criar um modelo de regressão GBT
Crie um modelo de regressão GBT utilizando a função Spark ML `GBTRegressor()` e, em seguida, avalie o modelo nos dados de teste.

[As árvores reforçadas com gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) são conjuntos de árvores de decisão. A GBTS treina a decisão das árvores iterativamente para minimizar uma função de perda. Pode utilizar GBTS para regressão e classificação. Pode manipular recursos categóricos, não requerem o dimensionamento do recurso e podem capturar nonlinearities e interações de funcionalidade. Também pode usá-los numa configuração de classificação de várias classes.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Saída:**

É de teste. o R-sqr: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitários de modelação avançada para Otimização
Nesta secção, vai utilizar utilitários de aprendizado de máquina que os desenvolvedores utilizam com frequência para a otimização de modelo. Especificamente, pode otimizar modelos de machine learning três formas diferentes com varrimento de parâmetro e a validação cruzada:

* Dividir os dados em conjuntos de formação e a validação, otimizar o modelo usando varrimento de hyper-parâmetro num conjunto de formação e avaliar num conjunto de validação (regressão linear)
* Otimizar o modelo com a validação cruzada e hyper varrimento através CrossValidator função do Spark ML (classificação binária) parâmetro
* Otimizar o modelo com o código de validação cruzada e varrimento de parâmetro personalizado para utilizar qualquer conjunto de função e o parâmetro (regressão linear) de aprendizagem automática

**A validação cruzada** é uma técnica que avalia o quão bem um modelo treinado num conjunto de dados conhecido se generalizará para prever as características dos conjuntos de dados em que não foi treinado. A idéia geral por trás dessa técnica é que é preparado um modelo num conjunto de dados de dados conhecidos e, em seguida, a precisão das previsões de seu é testada em relação a um conjunto de dados independente. Uma implementação comum édividir um conjunto de dados em k-folds, e depois treinar o modelo de forma redonda em todas, menos uma das dobras.

**A otimização do hiper-parâmetro** é o problema de escolher um conjunto de hiperparâmetros para um algoritmo de aprendizagem, geralmente com o objetivo de otimizar uma medida do desempenho do algoritmo num conjunto de dados independente. Um hyper-parâmetro é um valor que tem de especificar fora o procedimento de treinamento de modelo. Suposições sobre valores de parâmetro de hyper podem afetar a flexibilidade e a precisão do modelo. Árvores de decisões têm hiper parâmetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. Tem de definir um prazo de penalidade misclassification para uma máquina de vetor com suporte (SVM).

Uma forma comum de realizar a otimização do hiper-parâmetro é usar uma pesquisa de grelha, também chamada de varredura de **parâmetros.** Numa pesquisa de grade, uma pesquisa exaustiva é executada por meio dos valores de um determinado subconjunto do espaço de hyper-parâmetro para um algoritmo de aprendizagem. A validação cruzada pode fornecer uma métrica de desempenho para classificar os resultados ideais, produzidos pelo algoritmo de pesquisa de grade. Se usar varrimento de hyper-parâmetro de validação cruzada, pode ajudar a problemas de limite, como a overfitting um modelo de dados de treinamento. Dessa forma, o modelo mantém a capacidade para aplicar ao conjunto geral de dados a partir da qual os dados de treinamento foi extraídos.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Otimizar um modelo de regressão linear com hyper-parâmetro varrimento
Em seguida, dividir os dados em conjuntos de formação e a validação, utilize hyper-parâmetro varrimento num conjunto de treinamento para otimizar o modelo e avaliar num conjunto de validação (regressão linear).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Saída:**

É de teste. o R-sqr: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Otimizar o modelo de classificação binário, utilizando a validação cruzada e de hyper-parâmetro de varrimento
Esta secção mostra-lhe como otimizar um modelo de classificação binária utilizando varrimento de validação cruzada e de hyper-parâmetro. Isto utiliza a função de `CrossValidator` Spark ML.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: e 33 segundos.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Otimizar o modelo de regressão linear com o código de validação cruzada e varrimento de parâmetro personalizado
Em seguida, otimizar o modelo utilizando código personalizado e identificar os melhores parâmetros de modelo ao utilizar o critério de precisão mais elevada. Em seguida, crie o modelo final, avaliar o modelo em dados de teste e guardar o modelo no armazenamento de Blobs. Por fim, carregar o modelo, classificar os dados de teste e avaliar a precisão.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Saída:**

Tempo para executar a célula: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir modelos de aprendizagem de máquina criados com o Spark automaticamente com o Scala
Para uma visão geral dos tópicos que o percorrem através das tarefas que compõem o processo de Ciência de Dados em Azure, consulte o Processo de Ciência de [Dados da Equipa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Os walkthroughs do Team Data Science Process](walkthroughs.md) descrevem outros passadiços de ponta a ponta que demonstram os passos no Processo de Ciência de Dados da Equipa para cenários específicos. A instruções passo a passo também mostram como combinar as ferramentas de nuvem e no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente.

[Os modelos de machine learning construídos pela Spark mostram-lhe](spark-model-consumption.md) como usar o código Scala para carregar e marcar automaticamente novos conjuntos de dados com modelos de machine learning construídos em Spark e guardados no armazenamento azure Blob. Pode seguir as instruções fornecidas aqui e simplesmente substituir o código de Python com o Scala código neste artigo para consumo automatizado.

