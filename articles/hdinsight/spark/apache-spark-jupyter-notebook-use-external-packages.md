---
title: Use pacotes maven personalizados com Jupyter em Spark - Azure HDInsight
description: Instruções passo a passo sobre como configurar cadernos Jupyter disponíveis com clusters HDInsight Spark para usar pacotes Maven personalizados.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: a3d787059345b5971d99ebd7a1e26a9135b61bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930344"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Use pacotes externos com cadernos Jupyter em aglomerados Apache Spark em HDInsight

Aprenda a configurar um [Caderno Jupyter](https://jupyter.org/) em aglomerado Apache Spark em HDInsight para usar pacotes apache **maven** externos, contribuídos pela comunidade, que não estão incluídos fora da caixa no cluster.

Pode pesquisar no [repositório Maven](https://search.maven.org/) a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes com contribuições comunitárias está disponível nos [Pacotes Spark](https://spark-packages.org/).

Neste artigo, você vai aprender a usar o pacote [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o Caderno Jupyter.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Familiaridade com a utilização de Jupyter Notebooks com o Spark no HDInsight. Para obter mais informações, consulte [os dados de carga e execute consultas com Apache Spark em HDInsight](./apache-spark-load-data-run-query.md).

* O [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário dos seus clusters. Isto seria `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Azure Storage ou data lake storage gen2, o URI será `wasbs://` `abfss://` ou, respectivamente, ver também, [transferência segura](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Use pacotes externos com cadernos Jupyter

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` para onde está o nome do seu aglomerado de `CLUSTERNAME` faíscas.

1. Crie um novo bloco de notas. Selecione **Novo** e, em seguida, selecione **Spark**.

    ![Criar um novo Caderno Spark Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Criar um novo Caderno Jupyter")

1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Selecione o nome do portátil na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Fornecer um nome para o bloco de notas")

1. Vais usar a `%%configure` magia para configurar o caderno para usar um pacote externo. Em cadernos que usam pacotes externos, certifique-se de chamar a `%%configure` magia na primeira célula de código. Isto garante que o núcleo está configurado para utilizar a embalagem antes do início da sessão.

    >[!IMPORTANT]  
    >Se se esquecer de configurar o núcleo na primeira célula, pode usar o `%%configure` com o `-f` parâmetro, mas isso reiniciará a sessão e todos os progressos serão perdidos.

    | Versão HDInsight | Comando |
    |-------------------|---------|
    | Para HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Para HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. O corte acima espera as coordenadas maven para o pacote externo no Repositório Central de Maven. Neste corte, `com.databricks:spark-csv_2.11:1.5.0` está a coordenada maven para o pacote **spark-csv.** É assim que se constroem as coordenadas para um pacote.

    a. Localize o pacote no Repositório De Maven. Para este artigo, [usamos spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. A partir do repositório, reúna os valores para **GroupId,** **ArtifactId** e **Versão**. Certifique-se de que os valores que recolhe correspondem ao seu cluster. Neste caso, estamos a usar um pacote Scala 2.11 e Spark 1.5.0, mas poderá ter de selecionar diferentes versões para a versão adequada de Scala ou Spark no seu cluster. Você pode descobrir a versão Scala no seu cluster executando `scala.util.Properties.versionString` no kernel Spark Jupyter ou em Spark submit. Você pode descobrir a versão Spark no seu cluster correndo `sc.version` em Cadernos Jupyter.

    ![Use pacotes externos com Caderno Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Use pacotes externos com Caderno Jupyter")

    c. Concatenar os três valores, separados por um cólon **.**

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Executar a célula de código com a `%%configure` magia. Isto configurará a sessão livy subjacente para usar o pacote que forneceu. Nas células subsequentes do caderno, pode agora utilizar a embalagem, como mostra abaixo.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    Para HDInsight 3.4 e abaixo, deve utilizar o seguinte corte.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Em seguida, pode executar os snippets, como mostrado abaixo, para ver os dados do dataframe que criou no passo anterior.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Ver também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use pacotes de python externos com cadernos Jupyter em aglomerados apache spark em HDInsight Linux](apache-spark-python-package-installation.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight Plugin para IntelliJ IDEA para depurar as aplicações Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
