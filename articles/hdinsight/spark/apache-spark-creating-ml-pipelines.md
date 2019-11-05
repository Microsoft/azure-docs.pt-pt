---
title: Criar Apache Spark pipeline do Machine Learning-Azure HDInsight
description: Use a biblioteca de aprendizado de máquina Apache Spark para criar pipelines de dados no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494672"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Criar um pipeline de machine learning do Apache Spark

A MLlib (biblioteca de aprendizado de máquina escalonável) Apache Spark traz recursos de modelagem para um ambiente distribuído. O pacote Spark [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) é um conjunto de APIs de alto nível criadas com base em quadros de molduras. Essas APIs ajudam você a criar e ajustar pipelines práticos de aprendizado de máquina.  O *Spark Machine Learning* refere-se a essa API baseada em dataframe do MLlib, não a antiga API de pipeline baseada em RDD.

Um pipeline de ML (aprendizado de máquina) é um fluxo de trabalho completo que combina vários algoritmos de aprendizado de máquina juntos. Pode haver várias etapas necessárias para processar e aprender com os dados, exigindo uma sequência de algoritmos. Os pipelines definem os estágios e a ordenação de um processo de aprendizado de máquina. No MLlib, os estágios de um pipeline são representados por uma sequência específica de PipelineStages, em que um transformador e um estimador executam tarefas.

Um transformador é um algoritmo que transforma um dataframe para outro usando o método `transform()`. Por exemplo, um transformador de recurso pode ler uma coluna de um dataframe, mapeá-la para outra coluna e gerar um novo dataframe com a coluna mapeada anexada a ela.

Um estimador é uma abstração de algoritmos de aprendizado, e é responsável por ajustar ou treinar em um conjunto de informações para produzir um transformador. Um estimador implementa um método chamado `fit()`, que aceita um dataframe e produz um dataframe, que é um transformador.

Cada instância sem estado de um transformador ou um estimador tem seu próprio identificador exclusivo, que é usado ao especificar parâmetros. Ambos usam uma API uniforme para especificar esses parâmetros.

## <a name="pipeline-example"></a>Exemplo de pipeline

Para demonstrar um uso prático de um pipeline de ML, este exemplo usa o arquivo de dados de `HVAC.csv` de exemplo que vem pré-carregado no armazenamento padrão para seu cluster HDInsight, ou o armazenamento do Azure ou o Data Lake Storage. Para exibir o conteúdo do arquivo, navegue até o diretório `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` contém um conjunto de vezes com temperaturas de destino e reais para sistemas HVAC (*aquecimento, ventilação e ar-condicionado*) em vários prédios. O objetivo é treinar o modelo nos dados e produzir uma temperatura de previsão para um determinado edifício.

O código a seguir:

1. Define um `LabeledDocument`, que armazena o `BuildingID`, `SystemInfo` (identificador e idade do sistema) e uma `label` (1,0 se a compilação for muito quente, 0,0 caso contrário).
2. Cria uma função de analisador personalizada `parseDocument` que usa uma linha (linha) de dados e determina se a compilação é "quente", comparando a temperatura de destino com a temperatura real.
3. Aplica o analisador ao extrair os dados de origem.
4. Cria dados de treinamento.

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

Este pipeline de exemplo tem três estágios: `Tokenizer` e `HashingTF` (ambos os transformadores) e `Logistic Regression` (um estimador).  Os dados extraídos e analisados no `training` dataframe fluem pelo pipeline quando `pipeline.fit(training)` é chamado.

1. O primeiro estágio, `Tokenizer`, divide a coluna de entrada `SystemInfo` (consistindo no identificador do sistema e valores de idade) em uma `words` coluna de saída. Essa nova coluna `words` é adicionada ao dataframe. 
2. O segundo estágio, `HashingTF`, converte a nova coluna `words` em vetores de recursos. Essa nova coluna `features` é adicionada ao dataframe. Esses dois primeiros estágios são transformadores. 
3. O terceiro estágio, `LogisticRegression`, é um estimador e, portanto, o pipeline chama o método `LogisticRegression.fit()` para produzir uma `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Para ver as novas colunas `words` e `features` adicionadas pelos transformadores `Tokenizer` e `HashingTF`, e um exemplo do estimador de `LogisticRegression`, execute um método `PipelineModel.transform()` no dataframe original. No código de produção, a próxima etapa seria passar um dataframe de teste para validar o treinamento.

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

O objeto `model` agora pode ser usado para fazer previsões. Para obter o exemplo completo deste aplicativo de aprendizado de máquina e instruções passo a passo para executá-lo, consulte [criar Apache Spark aplicativos de Machine Learning no Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Consultar também

* [Ciência de dados usando escala e Apache Spark no Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
