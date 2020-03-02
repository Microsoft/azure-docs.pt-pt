---
title: Microsoft Cognitive Toolkit com Apache Spark - Azure HDInsight
description: Saiba como um modelo de aprendizagem profunda da Microsoft Cognitive Toolkit pode ser aplicado a um conjunto de dados utilizando a API Spark Python num cluster Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206559"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Use o modelo de aprendizagem profunda do Microsoft Cognitive Toolkit com o cluster Azure HDInsight Spark

Neste artigo, você faz os seguintes passos.

1. Execute um script personalizado para instalar o [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) num cluster De Spark Azure HDInsight.

2. Faça upload de um [Caderno Jupyter](https://jupyter.org/) para o cluster [Apache Spark](https://spark.apache.org/) para ver como aplicar um modelo de aprendizagem profunda de ferramentas cognitivas da Microsoft treinado para ficheiros numa Conta de Armazenamento Blob Azure utilizando a Spark [Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para mais informações, consulte [os dados de load e execute consultas com Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Como é que esta solução flui?

Esta solução divide-se entre este artigo e um caderno Jupyter que você carrega como parte deste artigo. Neste artigo, completa os seguintes passos:

* Execute uma ação de script num cluster HDInsight Spark para instalar os pacotes Microsoft Cognitive Toolkit e Python.
* Faça upload do caderno Jupyter que executa a solução para o cluster HDInsight Spark.

Os seguintes passos restantes estão cobertos no caderno jupyter.

* Carregue as imagens da amostra num Conjunto de Dados Distribuídos resiliente sinuoso ou RDD.
  * Carregue os módulos e defina predefinições.
  * Descarregue o conjunto de dados localmente no cluster Spark.
  * Converta o conjunto de dados num RDD.
* Marque as imagens usando um modelo de Toolkit Cognitivo treinado.
  * Descarregue o modelo de Toolkit Cognitivo treinado para o cluster Spark.
  * Defina funções a utilizar por nódosos operários.
  * Marque as imagens nos nódosos dos trabalhadores.
  * Avaliar a precisão do modelo.

## <a name="install-microsoft-cognitive-toolkit"></a>Instale o Kit de Ferramentas Cognitivas da Microsoft

Pode instalar o Microsoft Cognitive Toolkit num cluster Spark utilizando a ação do script. A ação do script utiliza scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Pode utilizar o script personalizado do portal Azure, utilizando o HDInsight .NET SDK ou utilizando o Azure PowerShell. Também pode utilizar o script para instalar o kit de ferramentas, seja como parte da criação do cluster, ou depois de o cluster estar a funcionar.

Neste artigo, utilizamos o portal para instalar o kit de ferramentas, depois de criado o cluster. Para outras formas de executar o script personalizado, consulte [os clusters Personalizados HDInsight utilizando](../hdinsight-hadoop-customize-cluster-linux.md)a Ação do Script .

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para obter instruções sobre como utilizar o portal Azure para executar a ação do script, consulte [Customize HDInsight clusters utilizando a Ação](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)do Script . Certifique-se de que fornece as seguintes inputs para instalar o Microsoft Cognitive Toolkit. Utilize os seguintes valores para a sua ação de script:

|Propriedade |Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome| Instalar MCT|
|Roteiro de bash URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Tipo de nó):|Cabeça, Trabalhador|
|Parâmetros|Nenhum|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Faça upload do caderno Jupyter para o cluster Azure HDInsight Spark

Para utilizar o Kit de Ferramentas Cognitivas da Microsoft com o cluster Azure HDInsight Spark, deve carregar o caderno Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** para o cluster Azure HDInsight Spark. Este caderno está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Descarregue e [desfaça https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration. ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)

1. De um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter`, onde `CLUSTERNAME` é o nome do seu cluster.

1. A partir do portátil Jupyter, selecione **Upload** no canto superior direito e, em seguida, navegue para o download e selecione `CNTK_model_scoring_on_Spark_walkthrough.ipynb`de ficheiros .

    ![Faça upload do caderno Jupyter para o cluster De faísca SOne HDInsight](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Faça upload do caderno Jupyter para o cluster De faísca SOne HDInsight")

1. **Selecione carregar** novamente.

1. Depois de o caderno ser carregado, clique no nome do caderno e siga as instruções do próprio caderno sobre como carregar o conjunto de dados e executar o artigo.

## <a name="see-also"></a>Consulte também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria insight de aplicação usando Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
