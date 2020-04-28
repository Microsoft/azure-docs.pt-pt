---
title: Crie o gasoduto de aprendizagem automática Apache Spark - Azure HDInsight
description: Utilize a biblioteca de machine learning Apache Spark para criar gasodutos de dados em Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73494672"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Criar um pipeline de machine learning do Apache Spark

A biblioteca de machine learning (MLlib) da Apache Spark traz capacidades de modelação para um ambiente distribuído. O pacote [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) Spark é um conjunto de APIs de alto nível construídos em DataFrames. Estas APIs ajudam-no a criar e a afinar os gasodutos práticos de aprendizagem automática.  *A spark machine learning* refere-se a este API baseado em MLlib DataFrame, e não ao mais antigo pipeline baseado em RDD API.

Um pipeline de aprendizagem automática (ML) é um fluxo de trabalho completo que combina vários algoritmos de aprendizagem automática. Pode haver muitos passos necessários para processar e aprender com dados, requerendo uma sequência de algoritmos. Os oleodutos definem as fases e a encomenda de um processo de aprendizagem automática. Em MLlib, as fases de um oleoduto são representadas por uma sequência específica de PipelineStages, onde um Transformador e um Estimador executam cada um tarefas.

Um Transformer é um algoritmo que transforma um `transform()` DataFrame para outro usando o método. Por exemplo, um transformador de funcionalidades poderia ler uma coluna de um DataFrame, mapeá-lo para outra coluna e fazer um novo DataFrame com a coluna mapeada anexada a ele.

Um Estimador é uma abstração de algoritmos de aprendizagem, e é responsável por encaixar ou treinar num conjunto de dados para produzir um Transformer. Um Estimador implementa um `fit()`método chamado , que aceita um DataFrame e produz um DataFrame, que é um Transformer.

Cada instância apátrida de um Transformador ou de um Estimador tem o seu próprio identificador único, que é usado ao especificar parâmetros. Ambos utilizam uma API uniforme para especificar estes parâmetros.

## <a name="pipeline-example"></a>Exemplo de gasoduto

Para demonstrar uma utilização prática de um `HVAC.csv` gasoduto ML, este exemplo utiliza o ficheiro de dados da amostra que vem pré-carregado no armazenamento predefinido para o seu cluster HDInsight, quer no Armazenamento De Azure quer no Armazenamento de Data Lake. Para ver o conteúdo do ficheiro, navegue para o `/HdiSamples/HdiSamples/SensorSampleData/hvac` diretório. `HVAC.csv`contém um conjunto de tempos com sistemas de avac e temperaturas reais para sistemas de AVAC *(aquecimento, ventilação e ar condicionado)* em vários edifícios. O objetivo é formar o modelo nos dados e produzir uma temperatura prevista para um determinado edifício.

O seguinte código:

1. Define um `LabeledDocument`, que `BuildingID` `SystemInfo` armazena o , (identificador `label` e idade de um sistema) e um (1.0 se o edifício estiver muito quente, 0.0 caso contrário).
2. Cria uma função `parseDocument` de parser personalizado que toma uma linha (linha) de dados e determina se o edifício está "quente" comparando a temperatura-alvo com a temperatura real.
3. Aplica o parser ao extrair os dados de origem.
4. Cria dados de treino.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Este gasoduto de exemplo `Tokenizer` `HashingTF` tem três fases: e (ambos Transformers) e `Logistic Regression` (um Estimador).  Os dados extraídos e analisados no `training` DataFrame `pipeline.fit(training)` fluem através do pipeline quando são chamados.

1. A primeira `Tokenizer`fase, divide `SystemInfo` a coluna de entrada (composta pelo identificador `words` do sistema e pelos valores de idade) numa coluna de saída. Esta `words` nova coluna é adicionada ao DataFrame. 
2. O segundo `HashingTF`estágio, converte `words` a nova coluna em vetores de recurso. Esta `features` nova coluna é adicionada ao DataFrame. Estes dois primeiros estágios são Transformers. 
3. A terceira `LogisticRegression`fase, é um Estimador, e por `LogisticRegression.fit()` isso o `LogisticRegressionModel`gasoduto chama o método de produzir a . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Para ver `words` as `features` novas e `Tokenizer` colunas adicionadas pelos transformadores e `HashingTF` transformadores, e uma amostra do `LogisticRegression` estimador, executar um `PipelineModel.transform()` método no DataFrame original. No código de produção, o próximo passo seria passar num dataFrame de teste para validar o treino.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

O `model` objeto pode agora ser usado para fazer previsões. Para obter a amostra completa desta aplicação de aprendizagem automática e instruções passo a passo para executá-la, consulte as aplicações de [aprendizagem automática Build Apache Spark no Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Consulte também

* [Data Science usando Scala e Apache Spark em Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
