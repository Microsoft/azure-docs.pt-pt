---
title: Microsoft Cognitive Toolkit com Apache Spark-HDInsight do Azure
description: Saiba como um modelo de aprendizado profundo Microsoft Cognitive Toolkit e treinado pode ser aplicado a um conjunto de informações usando a API do Python do Spark em um cluster Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 587de168a17c407abf3c2a7797969df013760a9f
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156646"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Usar Microsoft Cognitive Toolkit modelo de aprendizado profundo com Azure HDInsight Spark cluster

Neste artigo, você executa as etapas a seguir.

1. Execute um script personalizado para instalar [Microsoft cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) em um cluster Azure HDInsight Spark.

2. Carregar um [Jupyter Notebook](https://jupyter.org/) no cluster de [Apache Spark](https://spark.apache.org/) para ver como aplicar um modelo de aprendizado profundo de Microsoft cognitive Toolkit treinado a arquivos em uma conta de armazenamento de BLOBs do Azure usando a [API do Python do Spark (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Consulte [criar um cluster apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para obter mais informações, consulte [carregar dados e executar consultas com Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Como essa solução flui?

Esta solução é dividida entre este artigo e um notebook Jupyter que você carrega como parte deste artigo. Neste artigo, você concluirá as seguintes etapas:

* Execute uma ação de script em um cluster HDInsight Spark para instalar os pacotes Microsoft Cognitive Toolkit e Python.
* Carregue o notebook Jupyter que executa a solução para o cluster HDInsight Spark.

As etapas restantes a seguir são abordadas no notebook Jupyter.

* Carregue imagens de exemplo em um conjunto de RDDs distribuído e resiliente do Spark.
  * Carregar módulos e definir predefinições.
  * Baixe o conjunto de os localmente no cluster do Spark.
  * Converta o conjunto de um RDD.
* Pontuar as imagens usando um modelo de Cognitive Toolkit treinado.
  * Baixe o modelo de Cognitive Toolkit treinado para o cluster Spark.
  * Defina as funções a serem usadas por nós de trabalho.
  * Pontuar as imagens em nós de trabalho.
  * Avalie a precisão do modelo.

## <a name="install-microsoft-cognitive-toolkit"></a>Instalar Microsoft Cognitive Toolkit

Você pode instalar Microsoft Cognitive Toolkit em um cluster Spark usando a ação de script. A ação de script usa scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Você pode usar o script personalizado do portal do Azure, usando o SDK do .NET do HDInsight ou usando Azure PowerShell. Você também pode usar o script para instalar o kit de ferramentas como parte da criação do cluster ou depois que o cluster estiver em execução.

Neste artigo, usamos o portal para instalar o kit de ferramentas, após a criação do cluster. Para outras maneiras de executar o script personalizado, consulte [Personalizar clusters HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para obter instruções sobre como usar a portal do Azure para executar a ação de script, consulte [Personalizar clusters HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Certifique-se de fornecer as seguintes entradas para instalar o Microsoft Cognitive Toolkit. Use os seguintes valores para a ação de script:

|Propriedade |Valor |
|---|---|
|Tipo de script|-Personalizado|
|Nome| Instalar o MCT|
|URI do script bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Tipo (s) de nó:|Cabeçalho, trabalhador|
|Parâmetros|Nenhuma|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carregar o bloco de anotações Jupyter para Azure HDInsight Spark cluster

Para usar o Microsoft Cognitive Toolkit com o cluster Azure HDInsight Spark, você deve carregar o notebook Jupyter **CNTK_model_scoring_on_Spark_walkthrough. ipynb** no cluster Azure HDInsight Spark. Este bloco de anotações está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Baixar e descompactar [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter`, em que `CLUSTERNAME` é o nome do cluster.

1. No notebook Jupyter, selecione **carregar** no canto superior direito e, em seguida, navegue até o download e selecione Arquivo `CNTK_model_scoring_on_Spark_walkthrough.ipynb`.

    ![Carregar o bloco de anotações Jupyter para Azure HDInsight Spark cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Carregar o bloco de anotações Jupyter para Azure HDInsight Spark cluster")

1. Selecione **carregar** novamente.

1. Depois que o bloco de anotações for carregado, clique no nome do bloco de anotações e siga as instruções no próprio bloco de anotações sobre como carregar o conjunto de dados e executar o artigo.

## <a name="see-also"></a>Ver também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria do Application insights usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
