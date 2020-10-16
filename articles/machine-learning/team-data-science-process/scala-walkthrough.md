---
title: Data Science usando Scala e Spark on Azure - Processo de Ciência de Dados de Equipa
description: Como utilizar o Scala para tarefas de aprendizagem automática supervisionadas com os pacotes MLlib e Spark ML escaláveis de faíscas num cluster Azure HDInsight Spark.
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
ms.openlocfilehash: 56f266eaba76bb990a4d2bc3d902f4c5911d9c47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86026190"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Utilizar o Scala e o Spark para Ciência de Dados no Azure
Este artigo mostra-lhe como usar o Scala para tarefas de machine learning supervisionadas com os pacotes MLlib e Spark ML de spark num cluster Azure HDInsight Spark. Acompanha-o através das tarefas que constituem o [processo data Science](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelação e consumo de modelos. Os modelos do artigo incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas por gradientes (GBTs), além de duas tarefas comuns de aprendizagem automática supervisionada:

* Problema de regressão: Previsão do valor da gorjeta ($) para uma viagem de táxi
* Classificação binária: Previsão de ponta ou sem ponta (1/0) para uma viagem de táxi

O processo de modelação requer formação e avaliação num conjunto de dados de teste e métricas de precisão relevantes. Neste artigo, pode aprender a armazenar estes modelos no armazenamento da Azure Blob e como marcar e avaliar o seu desempenho preditivo. Este artigo também abrange os tópicos mais avançados de como otimizar os modelos usando a validação cruzada e a varredura de hiper-parâmetros. Os dados utilizados são uma amostra da viagem de táxi de NYC de 2013 e do conjunto de dados de tarifas disponíveis no GitHub.

[Scala](https://www.scala-lang.org/), uma linguagem baseada na máquina virtual Java, integra conceitos de linguagem funcional e orientados para objetos. É uma linguagem escalável que é bem adequada para o processamento distribuído na nuvem, e funciona em clusters Azure Spark.

[Spark](https://spark.apache.org/) é um quadro de processamento paralelo de código aberto que suporta o processamento na memória para aumentar o desempenho de aplicações de análise de big data. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para algoritmos iterativos em machine learning e computações de gráficos. O pacote [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) fornece um conjunto uniforme de APIs de alto nível construído em cima de quadros de dados que podem ajudá-lo a criar e afinar oleodutos práticos de aprendizagem de máquinas. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de machine learning escalável da Spark, que traz capacidades de modelação para este ambiente distribuído.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta azure-hospedada de Spark open-source. Também inclui suporte para cadernos Jupyter Scala no cluster Spark, e pode executar consultas interativas Spark SQL para transformar, filtrar e visualizar dados armazenados no armazenamento de Azure Blob. O código Scala corta neste artigo que fornecem as soluções e mostram as parcelas relevantes para visualizar os dados executados em cadernos Jupyter instalados nos clusters Spark. Os passos de modelação nestes tópicos têm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo.

Os passos de configuração e código deste artigo são para Azure HDInsight 3.4 Spark 1.6. No entanto, o código neste artigo e no [Bloco de Notas Scala Jupyter](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) são genéricos e devem funcionar em qualquer cluster Spark. A configuração do cluster e os passos de gestão podem ser ligeiramente diferentes do mostrado neste artigo se não estiver a utilizar o HDInsight Spark.

> [!NOTE]
> Para um tópico que lhe mostre como usar Python em vez de Scala para completar tarefas para um processo de ciência de dados de ponta a ponta, consulte [Data Science usando Spark on Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Precisa de uma subscrição do Azure. Se ainda não tiver um, [obtenha um julgamento gratuito do Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* Precisa de um cluster Azure HDInsight 3.4 Spark 1.6 para completar os seguintes procedimentos. Para criar um cluster, consulte as instruções em [Começar: Crie Apache Spark em Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Desa estar um modelo e versão de cluster no menu **Select Cluster Type.**

![Configuração do tipo de cluster HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Para obter uma descrição dos dados da viagem de táxi de NYC e instruções sobre como executar código a partir de um caderno Jupyter no cluster Spark, consulte as secções relevantes na [Visão Geral da Ciência dos Dados utilizando a Spark on Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Execute o código Scala a partir de um caderno Jupyter no cluster Spark
Pode lançar um caderno Jupyter a partir do portal Azure. Encontre o cluster Spark no seu painel de instrumentos e, em seguida, clique nele para introduzir a página de gestão para o seu cluster. Em seguida, clique em **Cluster Dashboards**e, em seguida, clique em **Jupyter Notebook** para abrir o caderno associado ao cluster Spark.

![Painel de painel de cluster e cadernos Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Também pode aceder aos cadernos jupyter https:// &lt; nome &gt; de clustername .azurehdinsight.net/jupyter. Substitua o *nome de cluster* pelo nome do seu cluster. Precisa da palavra-passe para a sua conta de administrador aceder aos cadernos do Jupyter.

![Vá aos cadernos de Jupyter usando o nome de cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecione **Scala** para ver um diretório que tem alguns exemplos de cadernos pré-embalados que usam a API PySpark. O Modelação e Pontuação de Exploração utilizando o caderno Scala.ipynb que contém as amostras de código para este conjunto de tópicos Spark está disponível no [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)

Pode fazer o upload do portátil diretamente do GitHub para o servidor Jupyter Notebook no seu cluster Spark. Na sua página inicial do Jupyter, clique no botão **Upload.** No explorador de ficheiros, cole o URL GitHub (conteúdo bruto) do caderno Scala e, em seguida, clique em **Abrir**. O caderno Scala está disponível no seguinte URL:

[Exploração-Modelação-e-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Configuração: Contextos de faíscas e colmeias predefinidas, magias spark e bibliotecas spark
### <a name="preset-spark-and-hive-contexts"></a>Contextos de faísca e colmeia predefinidas

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Os núcleos spark que são fornecidos com cadernos Jupyter têm contextos predefinidos. Não precisa de definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com a aplicação que está a desenvolver. Os contextos predefinidos são:

* `sc` para SparkContext
* `sqlContext` para HiveContext

### <a name="spark-magics"></a>Magias de faíscas
O kernel Spark fornece algumas "magias" predefinidas, que são comandos especiais com os quais se pode `%%` chamar. Dois destes comandos são usados nas seguintes amostras de código.

* `%%local` especifica que o código nas linhas subsequentes será executado localmente. O código deve ser o código Scala válido.
* `%%sql -o <variable name>` executa uma consulta de Colmeia contra `sqlContext` . Se o `-o` parâmetro for aprovado, o resultado da consulta é persistido no `%%local` contexto Scala como um quadro de dados spark.

Para obter mais informações sobre os núcleos dos cadernos Jupyter e as suas "magias" predefinidas que você chama `%%` (por exemplo, `%%local` ), consulte [Kernels disponível para cadernos Jupyter com clusters HDInsight Spark Linux em HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importar bibliotecas
Importe a Faísca, MLlib e outras bibliotecas que você precisará usando o seguinte código.

```scala
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
```

## <a name="data-ingestion"></a>Ingestão de dados
O primeiro passo no processo de Ciência de Dados é ingerir os dados que pretende analisar. Você traz os dados de fontes ou sistemas externos onde reside no seu ambiente de exploração e modelação de dados. Neste artigo, os dados que ingeriu são uma amostra de 0,1% da viagem de táxi e do ficheiro de tarifas (armazenado como ficheiro .tsv). O ambiente de exploração e modelação de dados é o Spark. Esta secção contém o código para completar a seguinte série de tarefas:

1. Definir caminhos de diretório para dados e armazenamento de modelos.
2. Leia no conjunto de dados de entrada (armazenado como ficheiro .tsv).
3. Defina um esquema para os dados e limpe os dados.
4. Crie uma moldura de dados limpa e cache-a na memória.
5. Registe os dados como uma tabela temporária no SQLContext.
6. Consultar a tabela e importar os resultados num quadro de dados.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir caminhos de diretório para locais de armazenamento em armazenamento Azure Blob
A faísca pode ler e escrever para o armazenamento da Azure Blob. Pode utilizar a Spark para processar qualquer um dos seus dados existentes e, em seguida, armazenar novamente os resultados no armazenamento blob.

Para guardar modelos ou ficheiros no armazenamento blob, é necessário especificar corretamente o caminho. Consulte o recipiente predefinido ligado ao cluster Spark utilizando um caminho que começa por `wasb:///` . Refira-se a outros locais utilizando `wasb://` .

A amostra de código que se segue especifica a localização dos dados de entrada a serem lidos e o caminho para o armazenamento blob que está ligado ao cluster Spark onde o modelo será guardado.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar dados, criar um RDD, e definir um quadro de dados de acordo com o esquema

```scala
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
```

**Saída:**

Hora de executar a cela: 8 segundos.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Consulta da tabela e importação resulta num quadro de dados
Em seguida, consultar a tabela para os dados de tarifas, passageiros e pontas; filtrar dados corruptos e sobressa esmuidos; e imprimir várias linhas.

```scala
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
```

**Saída:**

| fare_amount | passenger_count | tip_amount | gorjeta |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Exploração e visualização de dados
Depois de trazer os dados para a Spark, o próximo passo no processo de Data Science é obter uma compreensão mais profunda dos dados através da exploração e visualização. Nesta secção, você examina os dados do táxi usando consultas SQL. Em seguida, importe os resultados em um quadro de dados para traçar as variáveis-alvo e as funcionalidades prospetivas para inspeção visual usando a funcionalidade de visualização automática Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Use magia local e SQL para traçar dados
Por predefinição, a saída de qualquer corte de código que você executar a partir de um portátil Jupyter está disponível no contexto da sessão que é persistido nos nós do trabalhador. Se quiser guardar uma viagem aos nós dos trabalhadores para cada cálculo, e se todos os dados necessários para a sua computação estiverem disponíveis localmente no nó do servidor Jupyter (que é o nó da cabeça), pode utilizar `%%local` a magia para executar o corte de código no servidor Jupyter.

* **Magia SQL** ( `%%sql` ). O kernel HDInsight Spark suporta consultas de HiveQL fáceis em linha contra o SQLContext. O `-o VARIABLE_NAME` argumento ( ) persiste a saída da consulta SQL como um quadro de dados pandas no servidor Jupyter. Esta definição significa que a saída estará disponível no modo local.
* `%%local`**magia.** A `%%local` magia executa o código localmente no servidor Jupyter, que é o nó de cabeça do cluster HDInsight. Normalmente, usa-se `%%local` magia em conjunto com a magia com o `%%sql` `-o` parâmetro. O `-o` parâmetro persistiria na saída da consulta SQL localmente, e então `%%local` a magia desencadearia o próximo conjunto de código snippet para correr localmente contra a saída das consultas SQL que é persistido localmente.

### <a name="query-the-data-by-using-sql"></a>Consultar os dados utilizando o SQL
Esta consulta recupera as viagens de táxi por tarifa, contagem de passageiros e valor da gorjeta.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

No seguinte código, a `%%local` magia cria um quadro de dados local, sqlResults. Você pode usar sqlResults para tramar usando matplotlib.

> [!TIP]
> A magia local é usada várias vezes neste artigo. Se o seu conjunto de dados for grande, por favor, recolha para criar um quadro de dados que possa caber na memória local.
> 
> 

### <a name="plot-the-data"></a>Traçar os dados
Você pode traçar usando o código Python depois que o quadro de dados está no contexto local como um quadro de dados pandas.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 O núcleo spark visualiza automaticamente a saída de consultas SQL (HiveQL) depois de executar o código. Pode escolher entre vários tipos de visualizações:

* Tabela
* Circular
* Linha
* Área
* Barras

Aqui está o código para traçar os dados:

```scala
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
```

**Saída:**

![Histograma de quantidade de gorjeta](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Valor da gorjeta por contagem de passageiros](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Valor da gorjeta por tarifa](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Criar funcionalidades e transformar funcionalidades e, em seguida, preparar dados para a entrada em funções de modelação
Para funções de modelação baseadas em árvores de Spark ML e MLlib, você tem que preparar o alvo e as características usando uma variedade de técnicas, tais como a ligação, indexação, codificação de um quente e vectorização. Aqui estão os procedimentos a seguir nesta secção:

1. Crie uma nova **funcionalidade, vinculando** horas em baldes de tempo de tráfego.
2. Aplicar **indexação e codificação de um quente** a características categóricas.
3. **Recolher e dividir os dados definidos** em frações de treino e teste.
4. **Especifique a variável de formação e as características**, e, em seguida, crie treino codificado indexado ou de uma só vez e teste de entrada de pontos de distribuição resilientes (RDDs) ou quadros de dados.
5. **Categorize e vetorize** automaticamente funcionalidades e metas para usar como entradas para modelos de machine learning.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Crie uma nova funcionalidade ao ligar horas aos baldes de tempo de tráfego
Este código mostra-lhe como criar uma nova funcionalidade, ligando horas aos baldes de tempo de tráfego e como cache o quadro de dados resultante na memória. Onde os RDDs e os quadros de dados são usados repetidamente, o caching leva a tempos de execução melhorados. Assim, cache RDDs e quadros de dados em várias fases nos seguintes procedimentos.

```scala
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
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexação e codificação de características categóricas
As funções de modelação e previsão do MLlib requerem funcionalidades com dados de entrada categóricos para serem indexadas ou codificadas antes da utilização. Esta secção mostra-lhe como indexar ou codificar funcionalidades categóricas para a entrada nas funções de modelação.

É necessário indexar ou codificar os seus modelos de diferentes formas, dependendo do modelo. Por exemplo, os modelos logísticos e lineares de regressão requerem codificação única. Por exemplo, uma funcionalidade com três categorias pode ser expandida em três colunas de recurso. Cada coluna conteria 0 ou 1 dependendo da categoria de observação. A MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para codificação de um só hot. Este codificadora mapeia uma coluna de índices de etiquetas para uma coluna de vetores binários com, no máximo, um único valor único. Com esta codificação, os algoritmos que esperam características numéricas valorizadas, como a regressão logística, podem ser aplicados a características categóricas.

Aqui transforma-se apenas quatro variáveis para mostrar exemplos, que são cordas de carácter. Também pode indexar outras variáveis, como o dia da semana, representadas por valores numéricos, como variáveis categóricas.

Para indexar, utilizar `StringIndexer()` e para codificar uma só vez, utilize `OneHotEncoder()` funções da MLlib. Aqui está o código para indexar e codificar características categóricas:

```scala
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
```

**Saída:**

Hora de executar a cela: 4 segundos.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Amostra e divida os dados definidos em frações de treino e teste
Este código cria uma amostragem aleatória dos dados (25%, neste exemplo). Embora a amostragem não seja necessária para este exemplo devido ao tamanho do conjunto de dados, o artigo mostra como pode recolher amostras para que saiba como usá-la para os seus próprios problemas quando necessário. Quando as amostras são grandes, isto pode economizar tempo significativo enquanto treina modelos. Em seguida, divida a amostra numa parte de formação (75%, neste exemplo) e numa parte de teste (25%, neste exemplo) para utilizar na classificação e na modelação de regressão.

Adicione um número aleatório (entre 0 e 1) a cada linha (numa coluna "rand") que pode ser utilizado para selecionar dobras de validação cruzada durante o treino.

```scala
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
```

**Saída:**

Hora de executar a cela: 2 segundos.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especifique a variável e as características de formação e, em seguida, crie formação codificada indexada ou uma única vez e teste de entrada de pontos DEDS ou quadros de dados
Esta secção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto marcado, e codificá-lo para que possa usá-lo para treinar e testar a regressão logística MLlib e outros modelos de classificação. Os objetos de pontos rotulados são RDDs que são formatados de uma forma que é necessária como dados de entrada pela maioria dos algoritmos de aprendizagem automática em MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou escasso, associado a um rótulo/resposta.

Neste código, especifique a variável alvo (dependente) e as funcionalidades a utilizar para treinar modelos. Em seguida, cria treino codificado indexado ou de uma só brasa e testa RDDs de ponto marcados ou quadros de dados.

```scala
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
```

**Saída:**

Hora de executar a cela: 4 segundos.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorizar e vetoriar automaticamente características e metas para usar como entradas para modelos de aprendizagem automática
Utilize o Spark ML para categorizar o alvo e as características a utilizar em funções de modelação baseadas em árvores. O código completa duas tarefas:

* Cria um alvo binário para a classificação atribuindo um valor de 0 ou 1 a cada ponto de dados entre 0 e 1, utilizando um valor limiar de 0,5.
* Categoriza automaticamente funcionalidades. Se o número de valores numéricos distintos para qualquer característica for inferior a 32, essa característica é categorizada.

Aqui está o código para estas duas tarefas.

```scala
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
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de classificação binária: Prever se uma gorjeta deve ser paga
Nesta secção, cria-se três tipos de modelos de classificação binária para prever se uma gorjeta deve ou não ser paga:

* Um **modelo de regressão logística** utilizando a função Spark ML `LogisticRegression()`
* Um **modelo de classificação florestal aleatório** utilizando a função Spark ML `RandomForestClassifier()`
* Um **modelo de classificação de árvores de aumento de gradiente** utilizando a função MLlib `GradientBoostedTrees()`

### <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística
Em seguida, crie um modelo de regressão logística utilizando a função Spark `LogisticRegression()` ML. Cria-se o código de construção do modelo numa série de passos:

1. **Treine os** dados do modelo com um conjunto de parâmetros.
2. **Avalie o modelo** num conjunto de dados de teste com métricas.
3. **Guarde o modelo** no armazenamento Blob para consumo futuro.
4. **Marque o modelo** contra os dados do teste.
5. **Defina os resultados** com curvas de operação recetora (ROC).

Aqui está o código para estes procedimentos:

```scala
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
```

Carregue, marque e guarde os resultados.

```scala
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
```

**Saída:**

ROC em dados de teste = 0.9827381497557599

Use python em quadros de dados pandas locais para traçar a curva ROC.

```scala
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
```

**Saída:**

![Dica ou nenhuma curva ROC de ponta](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Criar um modelo de classificação florestal aleatório
Em seguida, crie um modelo de classificação florestal aleatório utilizando a função Spark ML `RandomForestClassifier()` e, em seguida, avalie o modelo em dados de teste.

```scala
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
```

**Saída:**

ROC em dados de teste = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Criar um modelo de classificação GBT
Em seguida, crie um modelo de classificação GBT utilizando a função de MLlib `GradientBoostedTrees()` e, em seguida, avalie o modelo em dados de teste.

```scala
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
```

**Saída:**

Área sob curva ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modelo de regressão: Prever o valor da gorjeta
Nesta secção, cria-se dois tipos de modelos de regressão para prever a quantidade de ponta:

* Um **modelo de regressão linear regularizado** utilizando a função Spark `LinearRegression()` ML. Irá guardar o modelo e avaliar o modelo nos dados de teste.
* Um **modelo de regressão de árvores que impulsiona o gradiente** utilizando a função Spark `GBTRegressor()` ML.

### <a name="create-a-regularized-linear-regression-model"></a>Criar um modelo regularizado de regressão linear

```scala
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
```


**Saída:**

Hora de executar a cela: 13 segundos.

```scala
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
```

**Saída:**

R-sqr em dados de teste = 0.5960320470835743

Em seguida, consultar os resultados do teste como uma moldura de dados e usar o AutoVizWidget e o matplotlib para visualizá-lo.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

O código cria um quadro de dados local a partir da saída de consulta e traça os dados. A `%%local` magia cria um quadro de dados `sqlResults` local, que pode usar para traçar com matplotlib.

> [!NOTE]
> Esta magia spark é usada várias vezes neste artigo. Se a quantidade de dados for grande, deve recolher amostras para criar um quadro de dados que possa caber na memória local.
> 
> 

Crie enredos usando python matplotlib.

```scala
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
```

**Saída:**

![Valor da gorjeta: Real vs. previsto](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Criar um modelo de regressão GBT
Crie um modelo de regressão GBT utilizando a função Spark ML `GBTRegressor()` e, em seguida, avalie o modelo em dados de teste.

[As árvores impulsionadas pelo gradiente](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) são conjuntos de árvores de decisão. O GBTS treina a decisão das árvores de forma iterativa para minimizar uma função de perda. Pode utilizar GBTS para regressão e classificação. Podem lidar com funcionalidades categóricas, não necessitam de escalagem de recursos e podem capturar não linearidades e interações de recurso. Também pode usá-los numa definição de classificação multiclasse.

```scala
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
```

**Saída:**

Teste R-sqr é: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitários de modelação avançados para otimização
Nesta secção, utiliza-se utilitários de aprendizagem automática que os desenvolvedores usam frequentemente para otimização de modelos. Especificamente, pode otimizar modelos de aprendizagem automática de três maneiras diferentes, utilizando a varredura de parâmetros e a validação cruzada:

* Divida os dados em conjuntos de comboio e validação, otimize o modelo utilizando a varredura de hiper-parâmetros num conjunto de treino e avalie num conjunto de validação (regressão linear)
* Otimize o modelo utilizando a validação cruzada e a varredura de hiper-parâmetros utilizando a função CrossValidator da Spark ML (classificação binária)
* Otimize o modelo utilizando o código de validação cruzada personalizada e de varredura de parâmetros para utilizar qualquer função de aprendizagem automática e conjunto de parâmetros (regressão linear)

**A validação cruzada** é uma técnica que avalia o quão bem um modelo treinado num conhecido conjunto de dados irá generalizar para prever as características dos conjuntos de dados em que não foi treinado. A ideia geral por trás desta técnica é que um modelo é treinado num conjunto de dados de dados conhecidos, e então a precisão das suas previsões é testada contra um conjunto de dados independente. Uma implementação comum é dividir *k*um conjunto de dados em k-folds, e depois treinar o modelo de uma forma redonda em todas as dobras.

**A otimização de hiper-parâmetros** é o problema de escolher um conjunto de hiper-parâmetros para um algoritmo de aprendizagem, geralmente com o objetivo de otimizar uma medida do desempenho do algoritmo num conjunto de dados independente. Um hiper-parâmetro é um valor que deve especificar fora do procedimento de treino do modelo. As suposições sobre os valores dos hiper-parâmetros podem afetar a flexibilidade e a precisão do modelo. As árvores de decisão têm hipermetrões, por exemplo, como a profundidade desejada e o número de folhas na árvore. Você deve definir um termo de penalização de classificação errada para uma máquina vetorial de suporte (SVM).

Uma forma comum de realizar a otimização de hiper-parâmetros é usar uma pesquisa de grelha, também chamada de **varredura de parâmetros.** Numa pesquisa de grelha, uma pesquisa exaustiva é realizada através dos valores de um subconjunto especificado do espaço hiper-parâmetro para um algoritmo de aprendizagem. A validação cruzada pode fornecer uma métrica de desempenho para resolver os resultados ideais produzidos pelo algoritmo de pesquisa da grelha. Se utilizar a varredura de hiper-parâmetros de validação cruzada, pode ajudar a limitar problemas como a adaptação excessiva de um modelo aos dados de treino. Desta forma, o modelo mantém a capacidade de aplicação ao conjunto geral de dados a partir do qual os dados de formação foram extraídos.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Otimizar um modelo linear de regressão com varredura de hiper-parâmetros
Em seguida, divida os dados em conjuntos de comboio e validação, use a varredura de hiper-parâmetros num conjunto de treino para otimizar o modelo e avalie num conjunto de validação (regressão linear).

```scala
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
```

**Saída:**

Teste R-sqr é: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Otimizar o modelo de classificação binária utilizando a validação cruzada e a varredura de hiper-parâmetros
Esta secção mostra-lhe como otimizar um modelo de classificação binária utilizando a validação cruzada e a varredura de hiper-parâmetros. Isto utiliza a `CrossValidator` função Spark ML.

```scala
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
```

**Saída:**

Hora de executar a cela: 33 segundos.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Otimize o modelo linear de regressão utilizando o código de validação cruzada personalizada e o código de varredura de parâmetros
Em seguida, otimize o modelo utilizando código personalizado e identifique os melhores parâmetros do modelo utilizando o critério de maior precisão. Em seguida, crie o modelo final, avalie o modelo em dados de teste e guarde o modelo no armazenamento blob. Por fim, carregue o modelo, marque dados de teste e avalie a precisão.

```scala
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
```

**Saída:**

Hora de executar a cela: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir modelos de aprendizagem automática construídos por faíscas automaticamente com o Scala
Para uma visão geral dos tópicos que o percorrem através das tarefas que compõem o processo de Ciência de Dados em Azure, consulte [o Processo de Ciência de Dados da Equipa.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

[O Team Data Science Process walkthroughs](walkthroughs.md) descreve outras caminhadas de ponta a ponta que demonstram os passos no Processo de Ciência de Dados da Equipa para cenários específicos. As caminhadas também ilustram como combinar ferramentas e serviços de nuvem e no local num fluxo de trabalho ou oleoduto para criar uma aplicação inteligente.

[Os modelos de machine learning construídos pela Score Spark mostram-lhe](spark-model-consumption.md) como usar o código Scala para carregar e marcar automaticamente novos conjuntos de dados com modelos de machine learning construídos em Spark e guardados no armazenamento Azure Blob. Pode seguir as instruções aí fornecidas e simplesmente substituir o código Python por código Scala neste artigo para consumo automatizado.

