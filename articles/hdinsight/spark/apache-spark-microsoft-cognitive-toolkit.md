---
title: Microsoft Cognitive Toolkit com Apache Spark - Azure HDInsight
description: Saiba como um modelo de aprendizagem profunda da Microsoft Cognitive Toolkit treinado pode ser aplicado a um conjunto de dados usando a API Spark Python num cluster Azure HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: cddbc4b6a5c7a2c787c8305fdf703e34543746f8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929969"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Use o modelo de aprendizagem profunda do Microsoft Cognitive Toolkit com cluster Azure HDInsight Spark

Neste artigo, faça os seguintes passos.

1. Execute um script personalizado para instalar o [Microsoft Cognitive Toolkit](/cognitive-toolkit/) num cluster Azure HDInsight Spark.

2. Faça o upload de um [Caderno Jupyter](https://jupyter.org/) para o cluster [Apache Spark](https://spark.apache.org/) para ver como aplicar um modelo de aprendizagem profunda do Microsoft Cognitive Toolkit treinado para ficheiros numa conta de armazenamento de blob Azure usando a [API spark Python (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Ver [Criar um aglomerado de faíscas Apache.](./apache-spark-jupyter-spark-sql-use-portal.md)

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para obter mais informações, consulte [os dados de carga e execute consultas com Apache Spark em HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Como é que esta solução flui?

Esta solução está dividida entre este artigo e um Caderno Jupyter que você carrega como parte deste artigo. Neste artigo, completa os seguintes passos:

* Execute uma ação de script num cluster HDInsight Spark para instalar pacotes Microsoft Cognitive Toolkit e Python.
* Faça o upload do Jupyter Notebook que executa a solução para o cluster HDInsight Spark.

Os seguintes passos restantes estão cobertos no Caderno Jupyter.

* Carregue as imagens da amostra num conjunto de dados distribuídos por faíscas ou RDD.
  * Carregue os módulos e defina predefinições.
  * Descarregue o conjunto de dados localmente no cluster Spark.
  * Converta o conjunto de dados num RDD.
* Marque as imagens usando um modelo de Ferramenta Cognitiva treinado.
  * Descarregue o modelo de Ferramentas Cognitivas treinado para o cluster Spark.
  * Defina funções a utilizar pelos nós dos trabalhadores.
  * Marque as imagens nos nós dos trabalhadores.
  * Avalie a precisão do modelo.

## <a name="install-microsoft-cognitive-toolkit"></a>Instalar o Microsoft Cognitive Toolkit

Pode instalar o Microsoft Cognitive Toolkit num cluster Spark utilizando ação do script. A ação do Script utiliza scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Pode utilizar o script personalizado a partir do portal Azure, utilizando HDInsight .NET SDK ou utilizando a Azure PowerShell. Também pode utilizar o script para instalar o conjunto de ferramentas, seja como parte da criação do cluster, quer depois de o cluster estar a funcionar.

Neste artigo, utilizamos o portal para instalar o conjunto de ferramentas, depois de o cluster ter sido criado. Para outras formas de executar o script personalizado, consulte [os clusters HdInsight personalizados utilizando a Ação do Script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para obter instruções sobre como usar o portal Azure para executar a ação de script, consulte [os clusters HDInsight personalizados utilizando a Ação do Script](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Certifique-se de que fornece as seguintes entradas para instalar o Microsoft Cognitive Toolkit. Utilize os seguintes valores para a sua ação de script:

|Propriedade |Valor |
|---|---|
|Tipo de script|- Personalizado|
|Name| Instalar MCT|
|URI de guião de bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Tipo de nó(s):|Cabeça, Trabalhador|
|Parâmetros|Nenhum|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Faça o upload do Caderno Jupyter para o cluster Azure HDInsight Spark

Para utilizar o Microsoft Cognitive Toolkit com o cluster Azure HDInsight Spark, tem de carregar o Bloco de Notas Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** para o cluster Azure HDInsight Spark. Este caderno está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Baixar e desapertar. [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. A partir do Bloco de Notas do Jupyter, **selecione Upload** no canto superior direito e, em seguida, navegue para o download e selecione o ficheiro `CNTK_model_scoring_on_Spark_walkthrough.ipynb` .

    ![Upload Jupyter Notebook para Azure HDInsight Spark cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Upload Jupyter Notebook para Azure HDInsight Spark cluster")

1. Selecione **upload** novamente.

1. Depois de o caderno ser carregado, clique no nome do caderno e siga as instruções no próprio caderno sobre como carregar o conjunto de dados e executar o artigo.

## <a name="see-also"></a>Veja também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados de telemetria de insight de aplicação usando Apache Spark em HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight Plugin para IntelliJ IDEA para depurar as aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
