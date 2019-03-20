---
title: Microsoft Cognitive Toolkit com o Azure HDInsight Spark para aprendizagem profunda
description: Saiba como um modelo de aprendizagem profunda da Microsoft Cognitive Toolkit preparado pode ser aplicado a um conjunto de dados utilizando a API de Python de Spark num cluster do Azure HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 093685ff2fa48799f0a91ee25727f5124dec6292
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225795"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Utilizar o Microsoft Cognitive Toolkit modelo com o cluster do Azure HDInsight Spark de aprendizagem profunda

Neste artigo, siga os passos abaixo.

1. Executar um script personalizado para instalar [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) num cluster do Azure HDInsight Spark.

2. Carregar uma [bloco de notas do Jupyter](https://jupyter.org/) para o [Apache Spark](https://spark.apache.org/) cluster para ver como aplicar um modelo de aprendizagem profunda da Microsoft Cognitive Toolkit preparado para ficheiros numa conta de armazenamento de Blobs do Azure com o [ Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

* **Cluster do Spark do HDInsight do Azure**. Neste artigo, crie um cluster do Spark 2.0. Para obter instruções, consulte [cluster de criar o Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Como fluxo esta solução?

Esta solução está dividida entre este artigo e um bloco de notas do Jupyter que carrega como parte deste tutorial. Neste artigo, conclua os seguintes passos:

* Execute uma ação de script num cluster do Spark do HDInsight para instalar o Microsoft Cognitive Toolkit e pacotes de Python.
* Carregar o bloco de notas do Jupyter que executa a solução para o cluster do HDInsight Spark.

As seguintes etapas restantes são abordadas no bloco de notas do Jupyter.

- Carregar imagens de exemplo para um conjunto de dados do Spark flexíveis distribuídas ou RDD.
   - Módulos de carga e definir as configurações predefinidas.
   - Transferir o conjunto de dados localmente no cluster do Spark.
   - Converta o conjunto de dados num RDD.
- Classificar as imagens com um modelo de conjunto de ferramentas cognitivas preparado.
   - Transfira o modelo de conjunto de ferramentas cognitivas preparado para o cluster do Spark.
   - Defina funções para serem utilizadas por nós de trabalho.
   - Classificar as imagens em nós de trabalho.
   - Avalie a precisão do modelo.


## <a name="install-microsoft-cognitive-toolkit"></a>Instalar o Microsoft Cognitive Toolkit

Pode instalar o Microsoft Cognitive Toolkit num cluster do Spark com ação de script. Ação de script utiliza scripts personalizados para instalar os componentes no cluster que não estão disponíveis por predefinição. Pode utilizar o script personalizado do portal do Azure, com o HDInsight .NET SDK, ou com o Azure PowerShell. Também pode utilizar o script para instalar o Kit de ferramentas como parte da criação do cluster, ou depois do cluster está em execução. 

Neste artigo, vamos utilizar o portal para instalar o Kit de ferramentas, depois do cluster ter sido criado. Para outras formas de executar o script personalizado, consulte [HDInsight personalizar clusters com ação de Script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para obter instruções sobre como utilizar o portal do Azure para executar a ação de script, consulte [HDInsight personalizar clusters com ação de Script](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Certifique-se de que fornece as entradas seguintes para instalar o Microsoft Cognitive Toolkit.

* Forneça um valor para o nome da ação de script.

* Para **URI do script de Bash**, introduza `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Certifique-se de que executa o script apenas os nós principais e de trabalho e desmarque todas as outras caixas de verificação.

* Clique em **Criar**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carregar o bloco de notas do Jupyter no cluster do Azure HDInsight Spark

Para utilizar o Microsoft Cognitive Toolkit com o cluster do Azure HDInsight Spark, deve carregar o bloco de notas do Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** ao cluster do Azure HDInsight Spark. Este bloco de notas está disponível no GitHub em [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Clonar o repositório do GitHub [ https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration ](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Para obter instruções clonar, consulte [clonar um repositório](https://help.github.com/articles/cloning-a-repository/).

2. A partir do portal do Azure, abra o painel de cluster do Spark que já aprovisionou, clique em **Dashboard de clusters**e, em seguida, clique em **bloco de notas do Jupyter**.

    Também pode iniciar o bloco de notas do Jupyter ao aceder ao URL `https://<clustername>.azurehdinsight.net/jupyter/`. Substitua \<clustername > com o nome do cluster do HDInsight.

3. No bloco de notas Jupyter, clique em **carregar** no canto superior direito canto e, em seguida, navegue para a localização onde clonou o repositório do GitHub.

    ![Carregar o bloco de notas do Jupyter no cluster do Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "bloco de notas do Jupyter carregar para o cluster do Azure HDInsight Spark")

4. Clique em **carregar** novamente.

5. Após o carregamento do bloco de notas, clique no nome do bloco de notas e, em seguida, siga as instruções no bloco de notas em si sobre como carregar o conjunto de dados e executar o tutorial.

## <a name="see-also"></a>Consulte também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise dados Application Insight telemetria com o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
