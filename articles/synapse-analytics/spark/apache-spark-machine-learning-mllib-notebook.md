---
title: Construa uma aplicação de machine learning com Apache Spark MLlib e Azure Synapse Analytics
description: Aprenda a usar o Apache Spark MLlib para criar uma aplicação de machine learning que analise um conjunto de dados usando a classificação através da regressão logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430009"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Construa uma aplicação de machine learning com Apache Spark MLlib e Azure Synapse Analytics

Neste artigo, aprende-se a usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar uma aplicação de machine learning que faz uma simples análise preditiva num conjunto de dados aberto do Azure. A Spark fornece bibliotecas de aprendizagem automática incorporadas. Este exemplo utiliza a *classificação* através da regressão logística.

MLlib é uma biblioteca core Spark que fornece muitos utilitários que são úteis para tarefas de aprendizagem automática, incluindo utilitários que são adequados para:

- Classificação
- Regressão
- Clustering
- Modelação de tópicos
- Decomposição de valor singular (SVD) e análise principal de componentes (PCA)
- Ensaio de hipóteses e cálculo das estatísticas da amostra

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e a regressão logística

*A classificação*, uma tarefa popular de aprendizagem automática, é o processo de classificação dos dados de entrada em categorias. É trabalho de um algoritmo de classificação descobrir como atribuir *etiquetas* aos dados de entrada que fornece. Por exemplo, pode pensar num algoritmo de aprendizagem automática que aceite a informação de stock como entrada e divide o stock em duas categorias: ações que deve vender e stocks que deve manter.

*A regressão logística* é um algoritmo que pode usar para a classificação. A API de regressão logística da Spark é útil para a *classificação binária,* ou classificando os dados de entrada em um de dois grupos. Para obter mais informações sobre regressões logísticas, consulte a [Wikipédia.](https://en.wikipedia.org/wiki/Logistic_regression)

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou ao outro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exemplo de análise preditiva nos dados do táxi de NYC

Neste exemplo, você usa Spark para realizar algumas análises preditivas sobre os dados de dicas de viagem de táxi de Nova Iorque. Os dados estão disponíveis através do [Azure Open Datasets.](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o tempo e os locais de início e fim, o custo e outros atributos interessantes.

> [!IMPORTANT]
> Pode haver encargos adicionais para retirar estes dados do seu local de armazenamento.

Nos seguintes passos, desenvolve-se um modelo para prever se uma determinada viagem inclui ou não uma dica.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie uma app de aprendizagem automática Apache Spark MLlib

1. Crie um caderno utilizando o kernel PySpark. Para obter as instruções, consulte [Criar um caderno](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook).
2. Importar os tipos necessários para este pedido. Copie e cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER,** ou execute a célula utilizando o ícone de reprodução azul à esquerda do código.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Por causa do kernel PySpark, você não precisa criar quaisquer contextos explicitamente. O contexto Spark é automaticamente criado para si quando executa a primeira célula de código.

## <a name="construct-the-input-dataframe"></a>Construir o quadro de dados de entrada

Como os dados brutos estão num formato Parquet, pode utilizar o contexto Spark para puxar o ficheiro para a memória como um quadro de dados diretamente. Enquanto o código abaixo utiliza as opções predefinidas, é possível forçar o mapeamento de tipos de dados e outros atributos de esquema, se necessário.

1. Executar as seguintes linhas para criar um quadro de dados Spark colando o código numa nova célula. A primeira secção atribui informações de acesso ao armazenamento do Azure a variáveis. A segunda secção permite que a Spark leia remotamente a partir do armazenamento de blob. A última linha de código diz parquet, mas nenhum dado é carregado neste momento.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Puxar todos estes dados gera cerca de 1,5 mil milhões de linhas. Dependendo do tamanho da sua piscina Spark (pré-visualização), os dados brutos podem ser demasiado grandes ou demorar muito tempo a operar. Pode filtrar estes dados para algo menor. Se necessário, adicione as seguintes linhas para filtrar os dados para cerca de 2 milhões de linhas para uma experiência mais responsiva. Use estes parâmetros para puxar uma semana de dados.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. A desvantagem para a simples filtragem é que, do ponto de vista estatístico, pode introduzir distorção nos dados. Outra abordagem é usar a amostragem incorporada em Spark. O código seguinte reduz o conjunto de dados para cerca de 2000 linhas, se aplicado após o código acima. Este passo de amostragem pode ser utilizado em vez do filtro simples ou em conjunto com o filtro simples.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Agora é possível olhar para os dados para ver o que foi lido. Normalmente, é melhor rever os dados com um subconjunto em vez do conjunto completo, dependendo do tamanho do conjunto de dados. O seguinte código oferece duas formas de visualizar os dados: o primeiro é básico e este último fornece uma experiência de grelha muito mais rica, bem como a capacidade de visualizar os dados graficamente.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Dependendo do tamanho do tamanho do conjunto de dados gerado e da sua necessidade de experimentar ou executar o caderno muitas vezes, pode ser aconselhável cache o conjunto de dados localmente no espaço de trabalho. Há três maneiras de realizar o cache explícito:

   - Guarde o dataframe localmente como um ficheiro
   - Guarde o quadro de dados como uma tabela ou vista temporária
   - Guarde o quadro de dados como uma tabela permanente

As primeiras duas abordagens estão incluídas nos seguintes exemplos de código.

A criação de uma tabela ou vista temporária proporciona diferentes vias de acesso aos dados, mas só dura durante a sessão de instância spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Compreender os dados

Normalmente, passaria por uma fase de análise de *dados exploratórios* (EDA) neste momento para desenvolver uma compreensão dos dados. O código seguinte mostra três visualizações diferentes dos dados relacionados com dicas que levam a conclusões sobre o estado e a qualidade dos dados.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Lote de](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![dispersão](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![de enredo de bigode de caixa histograma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparar os dados

Os dados na sua forma bruta não são frequentemente adequados para passar diretamente para um modelo. Uma série de ações devem ser realizadas nos dados para levá-lo a um estado em que o modelo possa consumi-lo.

No código abaixo de quatro classes de operações são realizadas:

- A remoção de valores outliers/incorretos através da filtragem.
- A remoção das colunas, que não são necessárias.
- A criação de novas colunas derivadas dos dados brutos para fazer o modelo funcionar de forma mais eficaz, por vezes chamada de caracterização.
- Rotulagem, uma vez que está a realizar uma classificação binária (haverá uma gorjeta ou não numa determinada viagem) é necessário converter o valor da gorjeta num valor de 0 ou 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Um segundo passe é então feito sobre os dados para adicionar as características finais.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística

A tarefa final é converter os dados rotulados num formato que possa ser analisado por regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares vetoriais de características de etiqueta,* onde o vetor de *características* é um vetor de números que representam o ponto de entrada. Então, precisamos converter as colunas categóricas em números. As `trafficTimeBins` `weekdayString` colunas e colunas têm de ser convertidas em representações inteiros. Existem múltiplas abordagens para realizar a conversão, no entanto a abordagem adotada neste exemplo é *oneHotEncoding*, uma abordagem comum.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Isto resulta num novo quadro de dados com todas as colunas no formato certo para treinar um modelo.

## <a name="train-a-logistic-regression-model"></a>Treine um modelo de regressão logística

A primeira tarefa é dividir o conjunto de dados num conjunto de treino e num conjunto de testes ou validação. A divisão aqui é arbitrária e você deve brincar com diferentes configurações divididas para ver se eles impactam o modelo.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Agora que existem dois DataFrames, a próxima tarefa é criar a fórmula do modelo e executá-la contra o dataframe de treino, em seguida, validá-lo contra o dataFrame de teste. Deve experimentar diferentes versões da fórmula do modelo para ver o impacto de diferentes combinações.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

A saída desta célula é

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão

Agora pode construir uma visualização final para ajudá-lo a raciocinar sobre os resultados deste teste. Uma [Curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) é uma forma de rever o resultado.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Curva ROC para modelo de ponta de regressão logística](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC para modelo de ponta de regressão logística")

## <a name="shut-down-the-spark-instance"></a>Desligue a instância Spark

Depois de ter terminado de executar a aplicação, desligue o caderno para libertar os recursos fechando o separador ou selecionando **a Sessão final** do painel de estado na parte inferior do caderno.

## <a name="see-also"></a>Consulte também

- [Visão geral: Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passos seguintes

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial de Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Parte da documentação oficial da Apache Spark baseia-se na utilização da consola Spark, que não está disponível no Azure Synapse Spark. Utilize o [caderno](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou as experiências [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
