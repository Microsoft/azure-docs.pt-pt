---
title: Utilizar pacotes personalizados do Maven com o Jupyter no Spark no Azure HDInsight
description: Obter instruções passo a passo sobre como configurar disponíveis da blocos de notas do Jupyter com clusters do Spark do HDInsight para utilizar pacotes personalizados do Maven.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: 6af25b95aa3a38c4a2e9f3bd8147604dccae0abb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715145"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilizar pacotes externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Magia de células](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Com a ação de Script](apache-spark-python-package-installation.md)

Saiba como configurar uma [bloco de notas do Jupyter](https://jupyter.org/) num cluster do Apache Spark no HDInsight para utilizar externo, fornecidos pela Comunidade para Apache **maven** pacotes que não são incluídos out of box, no cluster. 

Pode pesquisar o [repositório Maven](https://search.maven.org/) para obter a lista completa de pacotes que estão disponíveis. Também pode obter uma lista dos pacotes disponíveis a partir de outras origens. Por exemplo, está disponível numa lista completa dos pacotes fornecidos pela Comunidade [pacotes de Spark](https://spark-packages.org/).

Neste artigo, ficará a saber como utilizar o [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote com o bloco de notas do Jupyter.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter
1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   

1. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    > [!NOTE]  
    > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Crie um novo bloco de notas. Clique em **New**e, em seguida, clique em **Spark**.
   
    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Criar um novo bloco de notas Jupyter")

1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.
   
    ![Forneça um nome para o bloco de notas](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Forneça um nome para o bloco de notas")

1. Irá utilizar o `%%configure` mágica para configurar o bloco de notas para utilizar um pacote externo. Em blocos de notas que utilizam pacotes externos, certifique-se de que chama o `%%configure` mágico a primeira célula de código. Isto garante que o kernel está configurado para utilizar o pacote antes da sessão é iniciada.

    >[!IMPORTANT]  
    >Caso se esqueça de configurar o kernel na primeira célula, pode utilizar o `%%configure` com o `-f` parâmetro, mas que irá reiniciar a sessão e todo o progresso serão perdido.

    | Versão do HDInsight | Comando |
    |-------------------|---------|
    |Para o HDInsight 3.3 e 3.4 do HDInsight | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Para o HDInsight 3.5 e 3.6 do HDInsight | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. O fragmento acima espera que as coordenadas do maven para o pacote externo no repositório Central Maven. Neste trecho `com.databricks:spark-csv_2.10:1.4.0` é a coordenada do maven para **spark-csv** pacote. Eis como o construir as coordenadas de um pacote.
   
    a. Localize o pacote no repositório Maven. Para este tutorial, utilizamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A partir do repositório, reúna os valores para **GroupId**, **ArtifactId**, e **versão**. Certifique-se de que os valores que reúne corresponde ao seu cluster. Neste caso, estamos a utilizar um Scala 2.10 e um pacote de Spark 1.4.0, mas poderá ter de selecionar diferentes versões para o Scala apropriado ou a versão do Spark no seu cluster. Pode encontrar a versão de Scala no seu cluster através da execução `scala.util.Properties.versionString` no kernel do Jupyter do Spark ou no envio de Spark. Pode encontrar a versão do Spark no seu cluster através da execução `sc.version` nos blocos de notas do Jupyter.
   
    ![Utilizar pacotes externos com o bloco de notas do Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "utilizar pacotes externos com o bloco de notas do Jupyter")
   
    c. Concatenar os três valores separados por vírgula ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Executar a célula de código com o `%%configure` mágica. Isto irá configurar a sessão de Livy subjacente para utilizar o pacote que forneceu. Nas células subseqüentes no bloco de notas, agora, pode utilizar o pacote, conforme mostrado abaixo.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Para o HDInsight 3.6, deve usar o trecho a seguir.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Em seguida, pode executar os trechos de código, como mostrado abaixo, para ver os dados do dataframe que criou no passo anterior.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar pacotes de python externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight Linux](apache-spark-python-package-installation.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
