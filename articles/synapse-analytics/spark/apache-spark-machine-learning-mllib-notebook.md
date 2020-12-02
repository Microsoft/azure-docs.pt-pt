---
title: 'Tutorial: Construa uma app de machine learning com Apache Spark MLlib'
description: Um tutorial sobre como usar o Apache Spark MLlib para criar uma aplicação de machine learning que analisa um conjunto de dados usando a classificação através da regressão logística.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 50429696c4cbe10c4723f6d4bb9c9499d9b775c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450413"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Tutorial: Construa uma app de machine learning com Apache Spark MLlib e Azure Synapse Analytics

Neste artigo, você vai aprender a usar Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar uma aplicação de machine learning que faz uma simples análise preditiva num conjunto de dados aberto azure. A faísca fornece bibliotecas de aprendizagem automática embuti.. Este exemplo utiliza *a classificação* através da regressão logística.

SparkML e MLlib são bibliotecas core Spark que fornecem muitos utilitários que são úteis para tarefas de machine learning, incluindo utilitários que são adequados para:

- Classificação
- Regressão
- Clustering
- Modelação de tópicos
- Decomposição de valor singular (SVD) e análise principal de componentes (APC)
- Teste de hipóteses e cálculo das estatísticas da amostra

## <a name="understand-classification-and-logistic-regression"></a>Compreender classificação e regressão logística

*Classificação*, uma tarefa popular de aprendizagem automática, é o processo de triagem de dados de entrada em categorias. É função de um algoritmo de classificação descobrir como atribuir etiquetas aos *dados* de entrada que fornece. Por exemplo, pode pensar num algoritmo de machine learning que aceita informações de stock como entrada e divide o stock em duas categorias: ações que deve vender e ações que deve manter.

*A regressão logística* é um algoritmo que pode usar para a classificação. A API de regressão logística da Spark é útil para *a classificação binária,* ou para classificar dados de entrada em um de dois grupos. Para obter mais informações sobre regressões logísticas, consulte [a Wikipédia.](https://en.wikipedia.org/wiki/Logistic_regression)

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exemplo de análise preditiva sobre os dados do táxi de NYC

Neste exemplo, você usa Spark para realizar algumas análises preditivas em dados de dicas de viagem de táxi de Nova Iorque. Os dados estão disponíveis através [de Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Este subconjunto do conjunto de dados contém informações sobre viagens de táxi amarelas, incluindo informações sobre cada viagem, o tempo e locais de início e fim, o custo e outros atributos interessantes.

> [!IMPORTANT]
> Pode haver taxas adicionais para retirar estes dados do seu local de armazenamento.

Nos passos seguintes, desenvolve-se um modelo para prever se uma determinada viagem inclui uma dica ou não.

## <a name="create-an-apache-spark--machine-learning-model"></a>Crie um modelo de aprendizagem automática Apache Spark

1. Crie um caderno utilizando o núcleo PySpark. Para obter as instruções, consulte [Criar um caderno](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importe os tipos necessários para este pedido. Copie e cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER**, ou executar a célula utilizando o ícone de reprodução azul à esquerda do código.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Por causa do núcleo PySpark, não precisa de criar quaisquer contextos explicitamente. O contexto Spark é automaticamente criado para si quando executar a primeira célula de código.

## <a name="construct-the-input-dataframe"></a>Construa o dataframe de entrada

Como os dados brutos estão num formato Parquet, pode utilizar o contexto Spark para puxar o ficheiro para a memória como um dataframe diretamente. Embora o código abaixo utilize as opções predefinidos, é possível forçar o mapeamento de tipos de dados e outros atributos de esquema, se necessário.

1. Executar as seguintes linhas para criar um dataframe Spark colando o código numa nova célula. Isto recupera os dados através da API Open Datasets. Puxar todos estes dados gera cerca de 1,5 mil milhões de linhas. Dependendo do tamanho do seu conjunto Apache Spark sem servidor, os dados brutos podem ser demasiado grandes ou demorar muito tempo a operar. Pode filtrar estes dados para algo menor. O seguinte exemplo de código utiliza start_date e end_date para aplicar um filtro que devolve um único mês de dados.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. A desvantagem para a simples filtragem é que, do ponto de vista estatístico, pode introduzir distorções nos dados. Outra abordagem é usar a amostragem incorporada em Spark. O código seguinte reduz o conjunto de dados para cerca de 2000 linhas, se aplicado após o código acima. Este passo de amostragem pode ser utilizado em vez do filtro simples ou em conjunto com o filtro simples.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Agora é possível olhar para os dados para ver o que foi lido. Normalmente é melhor rever dados com um subconjunto em vez do conjunto completo, dependendo do tamanho do conjunto de dados. O código que se segue oferece duas formas de visualizar os dados: o primeiro sendo básico e este último proporcionando uma experiência de grelha muito mais rica, bem como a capacidade de visualizar os dados graficamente.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Dependendo do tamanho do tamanho do conjunto de dados gerado, e da sua necessidade de experimentar ou executar o caderno muitas vezes, pode ser aconselhável cache o conjunto de dados localmente no espaço de trabalho. Há três maneiras de realizar o caching explícito:

   - Guarde o dataframe localmente como um ficheiro
   - Guarde o dataframe como uma tabela ou vista temporária
   - Guarde o dataframe como tabela permanente

As duas primeiras abordagens estão incluídas nos seguintes exemplos de código.

A criação de uma tabela ou visualização temporária fornece diferentes caminhos de acesso aos dados, mas dura apenas a duração da sessão de exemplo spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Preparar os dados

Os dados na sua forma bruta não são frequentemente adequados para passar diretamente para um modelo. Uma série de ações deve ser realizada nos dados para levá-lo para um estado onde o modelo pode consumi-lo.

No código abaixo são realizadas quatro classes de operações:

- A remoção de valores outliers/incorretos através da filtragem.
- A remoção de colunas, que não são necessárias.
- A criação de novas colunas derivadas dos dados brutos para fazer o modelo funcionar de forma mais eficaz, por vezes chamada de caracterização.
- Rotulagem - uma vez que está a realizar uma classificação binária (haverá uma gorjeta ou não numa determinada viagem), é necessário converter o valor da gorjeta num valor de 0 ou 1.

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

A tarefa final é converter os dados rotulados num formato que possa ser analisado por regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de pares de *vetores de características de etiqueta,* onde o *vetor* de recurso é um vetor de números que representam o ponto de entrada. Então, precisamos converter as colunas categóricas em números. As `trafficTimeBins` `weekdayString` colunas e as colunas precisam de ser convertidas em representações inteiros. Existem múltiplas abordagens para realizar a conversão, no entanto a abordagem adotada neste exemplo é *OneHotEncoding*, uma abordagem comum.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Esta ação resulta num novo dataframe com todas as colunas no formato certo para formar um modelo.

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

Agora que existem dois DataFrames, a próxima tarefa é criar a fórmula do modelo e executá-la contra o DataFrame de treino, e depois validar contra o DataFrame de teste. Deve experimentar diferentes versões da fórmula do modelo para ver o impacto de diferentes combinações.

> [!Note]
> Para salvar o modelo, vai precisar da função Azure do Colaborador de Dados do Armazenamento Blob. Na sua conta de armazenamento, navegue para Access Control (IAM) e selecione **Adicionar a atribuição de funções**. Atribua o papel de Azure do Contribuinte de Dados de Armazenamento blob ao seu servidor SQL Database. Só os membros com privilégio proprietário podem realizar este passo. Para várias funções incorporadas do Azure, consulte este [guia.](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

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

A saída desta célula é:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão

Pode agora construir uma visualização final para ajudá-lo a raciocinar sobre os resultados deste teste. Uma [Curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) é uma forma de rever o resultado.

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

![Curva ROC para modelo de ponta de regressão logística](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Desligue o exemplo da Faísca

Depois de ter terminado a execução da aplicação, desligue o portátil para libertar os recursos fechando o separador ou selecione **Sessão final** do painel de estado na parte inferior do portátil.

## <a name="see-also"></a>Ver também

- [Visão geral: Apache Spark on Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passos seguintes

- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação oficial da Faísca Apache Spark](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Parte da documentação oficial do Apache Spark baseia-se na utilização da consola Spark, que não está disponível no Azure Synapse Spark. Utilize o [caderno](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ou as experiências [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
