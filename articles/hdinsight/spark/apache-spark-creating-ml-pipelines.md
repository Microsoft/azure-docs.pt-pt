---
title: Criar um Apache Spark de machine learning pipeline - Azure HDInsight
description: Utilize a biblioteca do Apache Spark do machine learning para criar pipelines de dados.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: c539460177a0a85938b886d161803e1fdf0e9e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124463"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Criar um pipeline de machine learning do Apache Spark

Biblioteca de rede do Apache Spark dimensionáveis de machine learning (MLlib) oferece capacidades de modelação para um ambiente distribuído. O pacote de Spark [ `spark.ml` ](https://spark.apache.org/docs/latest/ml-pipeline.html) é um conjunto de APIs de alto nível incorporadas no DataFrames. Estas APIs ajudam a criar e otimizar o práticos pipelines de machine learning.  *Aprendizagem do spark* refere-se a esta API com base em MLlib DataFrame, não mais antigo com base em RDD pipeline API.

Um pipeline (ML) de aprendizagem automática é um fluxo de trabalho completo, combinar vários algoritmos de machine learning em conjunto. Podem existir muitas etapas necessárias para processar e Aprenda com dados, exigindo que uma sequência de algoritmos. Pipelines definem as fases e a ordenação de um processo de aprendizagem automática. No MLlib, fases de um pipeline são representados por uma seqüência específica de PipelineStages, onde um transformador e um avaliador que esteja realizar tarefas.

Um transformador é um algoritmo que transforma um pacote de dados para outro, utilizando o `transform()` método. Por exemplo, um transformador recursos poderia ler uma coluna de um pacote de, mapeá-lo para outra coluna e um novo pacote de dados de saída com a coluna mapeada acrescentada.

Um avaliador que esteja é uma abstração de algoritmos de aprendizagem e é responsável por que se ajusta ou treinamento num conjunto de dados para produzir um transformador. Um avaliador que esteja implementa um método chamado `fit()`, que aceita um DataFrame e produz um pacote de dados, o que é um transformador.

Cada instância sem monitoração de estado de um transformador ou um avaliador que esteja tem seu próprio identificador exclusivo, que é utilizado ao especificar os parâmetros. Ambos utilizam uma API uniforme para especificar estes parâmetros.

## <a name="pipeline-example"></a>Exemplo de pipeline

Para demonstrar um uso prático de um pipeline de ML, este exemplo utiliza o exemplo `HVAC.csv` ficheiro de dados que estão pré-carregados no armazenamento padrão para o seu cluster do HDInsight, o armazenamento do Azure ou o armazenamento do Data Lake. Para ver o conteúdo do ficheiro, navegue para o `/HdiSamples/HdiSamples/SensorSampleData/hvac` diretório. `HVAC.csv` contém um conjunto de vezes com o destino e temperaturas reais de ar-Condicionado (*aquecimento, ventilação e ar condicionado*) sistemas em vários edifícios. O objetivo é preparar o modelo nos dados e produzir uma temperatura previsão para um determinado edifício.

O código a seguir:

1. Define um `LabeledDocument`, as lojas que o `BuildingID`, `SystemInfo` (um sistema identificador e idade) e um `label` (1.0, se a criação é muito quente, 0,0, caso contrário).
2. Cria uma função de analisador personalizado `parseDocument` que usa uma linha (linha) de dados e determina se a compilação é "quente" comparando a temperatura de destino para a temperatura real.
3. Aplica-se o analisador quando extrair os dados de origem.
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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Este pipeline de exemplo tem três fases: `Tokenizer` e `HashingTF` (ambas apresentam vários transformadores), e `Logistic Regression` (um avaliador que esteja).  Os dados extraídos e analisados a `training` DataFrame percorrem o pipeline quando `pipeline.fit(training)` é chamado.

1. O primeiro estágio, `Tokenizer`, divide o `SystemInfo` coluna de entrada (consistindo nos valores de identificador e a idade do sistema) num `words` coluna de saída. Este novo `words` coluna é adicionada para o pacote de dados. 
2. A segunda fase `HashingTF`, converte o novo `words` coluna em vetores de funcionalidade. Este novo `features` coluna é adicionada para o pacote de dados. Estas duas primeiras fases são apresentam vários transformadores. 
3. A terceira fase `LogisticRegression`, é um avaliador que esteja e, portanto, o pipeline chama o `LogisticRegression.fit()` método para produzir um `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Para ver a nova `words` e `features` colunas adicionadas pela `Tokenizer` e `HashingTF` apresentam vários transformadores e um exemplo do `LogisticRegression` avaliador, execute um `PipelineModel.transform()` método no pacote de dados original. No código de produção, a próxima etapa seria de passar num pacote de dados para validar o treinamento de teste.

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

O `model` objeto agora pode ser usado para fazer previsões. Para o exemplo completo desta aplicação e instruções passo a passo para executá-los de aprendizagem automática, consulte [Apache Spark criar aplicações de machine learning no Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Consulte também

* [Ciência de dados com o Scala e do Apache Spark no Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
