---
title: 'Tutorial: Criar um aplicação no Azure HDInsight de aprendizagem automática de Spark'
description: Instruções passo a passo para criar uma aplicação de machine learning do Apache Spark em clusters do Spark no HDInsight com o bloco de notas Jupyter.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 54ef9ac72ffeebbf228a85768f6c2eb4b64862c9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433404"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-hdinsight"></a>Tutorial: Criar uma Apache Spark aplicação de machine learning no HDInsight 

Neste tutorial, irá aprender a utilizar o [bloco de notas do Jupyter](https://jupyter.org/) para criar um [Apache Spark](https://spark.apache.org/) do machine learning a aplicação para o Azure HDInsight. 

[MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) é a biblioteca de machine learning dimensionável do Spark, constituída por utilitários e algoritmos de aprendizagem comuns, incluindo classificação, regressão, clustering, filtragem em colaboração, redução de dimensionalidade, bem como primitivos de otimização subjacentes.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Desenvolver uma aplicação de aprendizagem automática de Apache Spark

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos:

Tem de ter:

* Conclua [Criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="understand-the-data-set"></a>Compreender o conjunto de dados

A aplicação utiliza os dados de HVAC.csv de exemplo que estão disponíveis em todos os clusters por predefinição. O ficheiro está localizado em **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Os dados mostram a temperatura pretendida e a temperatura real de alguns edifícios que têm sistemas HVAC instalados. A coluna **System** (Sistema) representa o ID do sistema e a coluna **SystemAge** (Idade do sistema) representa há quanto anos o sistema HVAC foi implementado nesse edifício. Com estes dados, pode prever se um edifício vai estar mais quente ou frio com base na temperatura pretendida, num determinado ID de sistema e na idade do sistema.

![Instantâneo dos dados utilizados para o exemplo de machine learning do Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Instantâneo dos dados utilizados para o exemplo de machine learning do Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Desenvolver uma aplicação de machine learning do Spark com a MLlib do Spark

Nesta aplicação, vai utilizar um [pipeline de ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) do Spark para fazer a classificação de um documento. Os pipelines de ML proporcionam um conjunto uniforme de APIs de alto nível criadas sobre DataFrames que ajudam os utilizadores a criar e a ajustar pipelines de machine learning práticos. No pipeline, o documento é dividido em palavras, estas são convertidas num vetor de numérico de características e, por fim, é criado um modelo de previsão que utiliza os vetores de características e as etiquetas. Execute os seguintes passos para criar a aplicação.

1. Crie um bloco de notas do Jupyter com o kernel de PySpark. Para obter as instruções, veja [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importe os tipos necessários para este cenário. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Carregue os dados (hvac.csv), analise-os e utilize-os para preparar o modelo. 

    ```PySpark
    # Define a type called LabelDocument
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
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    No fragmento de código, vai definir uma função que compara a temperatura real com a temperatura pretendida. Se a temperatura real for superior, o edifício está quente, o que é mostrado pelo valor **1.0**. Caso contrário, o edifício está frio, o que é mostrado pelo valor **0.0**. 

4. Configure o pipeline de machine learning do Spark, que consiste em três etapas: tokenizer, hashingTF e lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Para obter mais informações sobre o pipeline e como ele funciona, consulte <a href="https://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">pipeline de machine learning do Apache Spark</a>.

5. Ajuste o pipeline ao documento de preparação.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Verifique o documento de preparação para registar o progresso com a aplicação.
   
    ```PySpark
    training.show()
    ```
   
    O resultado é semelhante a:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Compare o resultado com o ficheiro CSV não processado. Por exemplo, a primeira linha do ficheiro CSV tem estes dados:

    ![Instantâneo dos dados de saída do exemplo de machine learning do Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Instantâneo dos dados de saída do exemplo de machine learning do Spark")

    Repare como a temperatura real é inferior à pretendida, o que sugere que o edifício está frio. Por conseguinte, na saída da preparação, o valor de **label** (etiqueta) na primeira linha é **0.0**, o que significa que o edifício não está quente.

7. Prepare um conjunto de dados no qual executar o modelo preparado. Para tal, transmita um ID de sistema e uma idade de sistema (apresentados como **SystemInfo** (Informações do Sistema) na saída da preparação) e o modelo preverá se o edifício que corresponde a essas informações estará mais quente (indicado por 1.0) ou mais frio (indicado por 0.0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Por fim, faça predições nos dados de teste. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    O resultado é semelhante a:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Na primeira linha da predição, pode ver que, para o sistema HVAC com o ID 20 e a idade de sistema de 25 anos, o edifício está quente (**prediction=1.0** (predição=1.0)). O primeiro valor para DenseVector (0.49999) corresponde à predição 0.0 e o segundo valor (0.5001) corresponde à predição 1.0. Na saída, apesar de o segundo valor ser apenas marginalmente superior, o modelo mostra **prediction=1.0**.
10. Encerre o bloco de notas para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Esta ação encerra e fecha o bloco de notas.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Utilizar a biblioteca scikit-learn de Anaconda para machine learning do Spark
Os clusters do Apache Spark no HDInsight incluem bibliotecas de Anaconda. Também inclui a biblioteca **scikit-learn** para machine learning. Esta biblioteca inclui igualmente vários conjuntos de dados que pode utilizar para criar aplicações de exemplo diretamente a partir de blocos de notas do Jupyter. Para obter exemplos de como utilizar a biblioteca scikit-learn, veja [ https://scikit-learn.org/stable/auto_examples/index.html ](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

* Desenvolver uma aplicação de aprendizagem automática de Apache Spark

Avance para o próximo tutorial para saber como utilizar o IntelliJ IDEA para trabalhos do Spark. 

> [!div class="nextstepaction"]
> [Criar uma aplicação Scala Maven com o IntelliJ](./apache-spark-create-standalone-application.md)

