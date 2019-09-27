---
title: Usar pacotes Maven personalizados com Jupyter no Spark no Azure HDInsight
description: Instruções passo a passo sobre como configurar notebooks Jupyter disponíveis com clusters Spark do HDInsight para usar pacotes Maven personalizados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: cd88c85c927c635269d814c20b15f574212e1a6d
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337679"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usar pacotes externos com blocos de anotações do Jupyter em clusters Apache Spark no HDInsight

> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

Saiba como configurar um [Jupyter Notebook](https://jupyter.org/) no cluster apache Spark no HDInsight para usar pacotes Apache **Maven** externos e contribuídos pela Comunidade que não estão incluídos de forma integrada no cluster. 

Você pode pesquisar o [repositório do Maven](https://search.maven.org/) para obter a lista completa de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes contribuídos pela Comunidade está disponível em [pacotes do Spark](https://spark-packages.org/).

Neste artigo, você aprenderá a usar o pacote [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o notebook Jupyter.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter

1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.

1. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    > [!NOTE]  
    > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Crie um novo bloco de notas. Clique em **novo**e em **Spark**.
   
    ![Criar um novo notebook Spark Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "criar um novo notebook Jupyter")

1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.
   
    ![Forneça um nome para o bloco de notas](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Forneça um nome para o bloco de notas")

1. Você usará a mágica `%%configure` para configurar o bloco de anotações para usar um pacote externo. Em blocos de anotações que usam pacotes externos, certifique-se de chamar a mágica `%%configure` na primeira célula de código. Isso garante que o kernel esteja configurado para usar o pacote antes de iniciar a sessão.

    >[!IMPORTANT]  
    >Se você se esquecer de configurar o kernel na primeira célula, poderá usar o `%%configure` com o parâmetro `-f`, mas isso reiniciará a sessão e todo o progresso será perdido.

    | Versão do HDInsight | Comando |
    |-------------------|---------|
    |Para HDInsight 3,3 e HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Para HDInsight 3,5 e HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. O trecho acima espera as coordenadas do Maven para o pacote externo no repositório central do Maven. Neste trecho de código, `com.databricks:spark-csv_2.10:1.4.0` é a coordenada do Maven para o pacote **Spark-CSV** . Veja como construir as coordenadas de um pacote.
   
    a. Localize o pacote no repositório do Maven. Para este artigo, usamos o [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. No repositório, reúna os valores para **GroupId**, **artefatoid**e **versão**. Certifique-se de que os valores coletados correspondam ao cluster. Nesse caso, estamos usando um pacote escalar 2,10 e Spark 1.4.0, mas talvez seja necessário selecionar versões diferentes para a versão escala ou Spark apropriada no cluster. Você pode descobrir a versão do escala em seu cluster executando `scala.util.Properties.versionString` no kernel do Spark Jupyter ou no envio do Spark. Você pode descobrir a versão do Spark no cluster executando `sc.version` em notebooks Jupyter.
   
    ![Usar pacotes externos com o Jupyter Notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Usar pacotes externos com o Jupyter Notebook")
   
    c. Concatene os três valores, separados por dois-pontos ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Execute a célula de código com a mágica `%%configure`. Isso irá configurar a sessão Livy subjacente para usar o pacote fornecido. Nas células subsequentes no bloco de anotações, agora você pode usar o pacote, conforme mostrado abaixo.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Para o HDInsight 3,6, você deve usar o trecho a seguir.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Em seguida, você pode executar os trechos de código, como mostrado abaixo, para exibir os dados do dataframe que você criou na etapa anterior.

        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Ver também

* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar pacotes python externos com notebooks Jupyter em clusters de Apache Spark no HDInsight Linux](apache-spark-python-package-installation.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
