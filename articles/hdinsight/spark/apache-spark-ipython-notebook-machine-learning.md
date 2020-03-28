---
title: 'Tutorial: Build Spark machine learning app - Azure HDInsight'
description: Tutorial - Instruções passo a passo sobre como construir a aplicação de aprendizagem automática Apache Spark em clusters HDInsight Spark usando o portátil Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: 6e46d7403e251bccd69467cfcdaa1d5073b4e454
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494555"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Tutorial: Construa uma aplicação de aprendizagem automática Apache Spark em Azure HDInsight

Neste tutorial, você aprende a usar o [Jupyter Notebook](https://jupyter.org/) para construir uma aplicação de aprendizagem automática [Apache Spark](https://spark.apache.org/) para Azure HDInsight.

[MLib](https://spark.apache.org/docs/latest/ml-guide.html) é a biblioteca de machine learning dimensionável do Spark, constituída por utilitários e algoritmos de aprendizagem comuns, incluindo classificação, regressão, clustering, filtragem em colaboração, redução de dimensionalidade, bem como primitivos de otimização subjacentes.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Desenvolver uma aplicação de aprendizagem automática Apache Spark

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para mais informações, consulte [os dados de load e execute consultas com Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Compreender o conjunto de dados

A aplicação utiliza os dados da amostra **HVAC.csv** que estão disponíveis em todos os clusters por padrão. O ficheiro está `\HdiSamples\HdiSamples\SensorSampleData\hvac`localizado a . Os dados mostram a temperatura pretendida e a temperatura real de alguns edifícios que têm sistemas HVAC instalados. A coluna **System** (Sistema) representa o ID do sistema e a coluna **SystemAge** (Idade do sistema) representa há quanto anos o sistema HVAC foi implementado nesse edifício. Com estes dados, pode prever se um edifício vai estar mais quente ou frio com base na temperatura pretendida, num determinado ID de sistema e na idade do sistema.

![Instantâneo de dados utilizados para o exemplo de aprendizagem automática spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Instantâneo de dados utilizados para o exemplo de aprendizagem automática spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Desenvolver uma aplicação de machine learning do Spark com a MLlib do Spark

Nesta aplicação, vai utilizar um [pipeline de ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) do Spark para fazer a classificação de um documento. Os pipelines de ML proporcionam um conjunto uniforme de APIs de alto nível criadas sobre DataFrames que ajudam os utilizadores a criar e a ajustar pipelines de machine learning práticos. No pipeline, o documento é dividido em palavras, estas são convertidas num vetor de numérico de características e, por fim, é criado um modelo de previsão que utiliza os vetores de características e as etiquetas. Execute os seguintes passos para criar a aplicação.

1. Crie um bloco de notas do Jupyter com o kernel de PySpark. Para obter as instruções, veja [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

1. Importe os tipos necessários para este cenário. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

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

1. Carregue os dados (hvac.csv), analise-os e utilize-os para preparar o modelo.

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

1. Configure o pipeline de machine learning do Spark, que consiste em três etapas: tokenizer, hashingTF e lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Para obter mais informações sobre o oleoduto e como funciona, consulte o [gasoduto de aprendizagem automática Apache Spark.](https://spark.apache.org/docs/latest/ml-pipeline.html)

1. Ajuste o pipeline ao documento de preparação.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Verifique o documento de preparação para registar o progresso com a aplicação.

    ```PySpark
    training.show()
    ```

    O resultado é semelhante a:

    ```output
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

    ![Instantâneo de dados de saída para exemplo de aprendizagem automática spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Instantâneo de dados de saída para exemplo de aprendizagem automática spark")

    Repare como a temperatura real é inferior à pretendida, o que sugere que o edifício está frio. Por conseguinte, na saída da preparação, o valor de **label** (etiqueta) na primeira linha é **0.0**, o que significa que o edifício não está quente.

1. Prepare um conjunto de dados no qual executar o modelo preparado. Para tal, transmita um ID de sistema e uma idade de sistema (apresentados como **SystemInfo** (Informações do Sistema) na saída da preparação) e o modelo preverá se o edifício que corresponde a essas informações estará mais quente (indicado por 1.0) ou mais frio (indicado por 0.0).

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

1. Por fim, faça predições nos dados de teste.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    O resultado é semelhante a:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Na primeira linha da predição, pode ver que, para o sistema HVAC com o ID 20 e a idade de sistema de 25 anos, o edifício está quente (**prediction=1.0** (predição=1.0)). O primeiro valor para DenseVector (0.49999) corresponde à predição 0.0 e o segundo valor (0.5001) corresponde à predição 1.0. Na saída, apesar de o segundo valor ser apenas marginalmente superior, o modelo mostra **prediction=1.0**.

1. Encerre o bloco de notas para libertar os recursos. Para tal, no menu **File** (Ficheiro) do bloco de notas, selecione **Close and Halt** (Fechar e Parar). Esta ação encerra e fecha o bloco de notas.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Utilizar a biblioteca scikit-learn de Anaconda para machine learning do Spark

Os clusters do Apache Spark no HDInsight incluem bibliotecas de Anaconda. Também inclui a biblioteca **scikit-learn** para machine learning. Esta biblioteca inclui igualmente vários conjuntos de dados que pode utilizar para criar aplicações de exemplo diretamente a partir de blocos de notas do Jupyter. Por exemplo, na utilização da biblioteca [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html)de ficção científica, ver .

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Sim**.

![Portal Azure apaga um cluster HDInsight](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster HDInsight")

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o Caderno Jupyter para construir uma aplicação de aprendizagem automática Apache Spark para o Azure HDInsight. Avance para o próximo tutorial para saber como utilizar o IntelliJ IDEA para trabalhos do Spark.

> [!div class="nextstepaction"]
> [Criar uma aplicação Scala Maven usando o IntelliJ](./apache-spark-create-standalone-application.md)
