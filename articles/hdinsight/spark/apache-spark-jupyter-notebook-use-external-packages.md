---
title: Utilize pacotes Maven personalizados com Jupyter em Spark - Azure HDInsight
description: Instruções passo a passo sobre como configurar os cadernos Jupyter disponíveis com clusters HDInsight Spark para usar pacotes Maven personalizados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561711"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilize pacotes externos com cadernos Jupyter em clusters Apache Spark no HDInsight

> [!div class="op_single_selector"]
> * [Usando magia celular](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação do script](apache-spark-python-package-installation.md)

Aprenda a configurar um [Caderno Jupyter](https://jupyter.org/) no cluster Apache Spark no HDInsight para usar pacotes apache **maven** externos e com contribuições comunitárias que não estão incluídos fora da caixa no cluster.

Pode pesquisar o [repositório Maven](https://search.maven.org/) para obter a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes com contribuições comunitárias está disponível nos [Pacotes Spark](https://spark-packages.org/).

Neste artigo, você vai aprender a usar o pacote [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o caderno Jupyter.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para mais informações, consulte [os dados de load e execute consultas com Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o `abfs://` Armazenamento Azure, para o `adl://` Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento de `wasbs://` Azure ou data Lake Storage Gen2, o URI seria ou, `abfss://`respectivamente, ver também, [transferência segura](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` `CLUSTERNAME` para onde está o nome do seu aglomerado de faíscas.

1. Crie um novo bloco de notas. Selecione **Novo**, e, em seguida, selecione **Spark**.

    ![Crie um novo caderno Spark Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Criar um novo bloco de notas do Jupyter")

1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Selecione o nome do caderno na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Fornecer um nome para o bloco de notas")

1. Usará a `%%configure` magia para configurar o caderno para usar um pacote externo. Nos cadernos que utilizam pacotes externos, certifique-se de chamar a `%%configure` magia na primeira célula de código. Isto garante que o núcleo está configurado para utilizar a embalagem antes do início da sessão.

    >[!IMPORTANT]  
    >Se se esquecer de configurar o núcleo na primeira `%%configure` célula, pode utilizar o com o `-f` parâmetro, mas isso reiniciará a sessão e todos os progressos serão perdidos.

    | Versão HDInsight | Comando |
    |-------------------|---------|
    | Para HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Para HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. O corte acima espera as coordenadas maven para o pacote externo no Repositório Central de Maven. Neste corte, `com.databricks:spark-csv_2.11:1.5.0` está a coordenada maven para o pacote **spark-csv.** É assim que se constrói as coordenadas para um pacote.

    a. Localize o pacote no Repositório Maven. Para este artigo, usamos [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Do repositório, recolha os valores para **GroupId,** **ArtifactId,** e **Versão**. Certifique-se de que os valores que recolhe correspondem ao seu cluster. Neste caso, estamos a usar um pacote Scala 2.11 e Spark 1.5.0, mas poderá ter de selecionar diferentes versões para a versão Scala ou Spark apropriada no seu cluster. Você pode descobrir a versão Scala `scala.util.Properties.versionString` no seu cluster executando no kernel Spark Jupyter ou no spark submit. Você pode descobrir a versão Spark `sc.version` no seu cluster executando em cadernos Jupyter.

    ![Use pacotes externos com caderno Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Use pacotes externos com caderno Jupyter")

    c. Concatenaos os três valores, separados por um cólon (**:**).

        com.databricks:spark-csv_2.11:1.5.0

1. Executa a célula `%%configure` de código com a magia. Isto configurará a sessão livy subjacente para utilizar o pacote que forneceu. Nas células seguintes do caderno, pode agora utilizar o pacote, como mostrado abaixo.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Para HDInsight 3.4 e abaixo, deve utilizar o seguinte corte.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Em seguida, pode executar os cortes, como mostrado abaixo, para visualizar os dados a partir do dataframe que criou no passo anterior.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Consulte também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize pacotes de pitão externos com cadernos Jupyter em clusters Apache Spark em HDInsight Linux](apache-spark-python-package-installation.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Plugin de Ferramentas HDInsight para intelliJ IDEA para depurar aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
