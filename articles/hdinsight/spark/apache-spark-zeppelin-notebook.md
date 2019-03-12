---
title: Utilizar blocos de notas do Zeppelin com o cluster do Apache Spark no Azure HDInsight
description: Instruções passo a passo sobre como utilizar blocos de notas do Zeppelin com clusters do Apache Spark no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 019232308ec5fa6d735e4499c3fb5f3ac2727e2d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766399"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utilizar blocos de notas do Zeppelin do Apache com o cluster do Apache Spark no Azure HDInsight

Os clusters do Spark do HDInsight incluem [Apache Zeppelin](https://zeppelin.apache.org/) blocos de notas que pode utilizar para executar [Apache Spark](https://spark.apache.org/) tarefas. Neste artigo, irá aprender a utilizar o bloco de notas do Zeppelin num cluster do HDInsight.

**Pré-requisitos:**

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* O esquema URI para o armazenamento primário de clusters. Isso seria `wasb://` para o armazenamento de Blobs do Azure, `abfs://` para a geração 2 de armazenamento do Azure Data Lake ou `adl://` para geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento de BLOBs ou de geração 2 de armazenamento do Data Lake, o URI seria `wasbs://` ou `abfss://`, respectivamente.  Consulte também [requer transferência segura no armazenamento do Azure](../../storage/common/storage-require-secure-transfer.md) para obter mais informações.

## <a name="launch-an-apache-zeppelin-notebook"></a>Iniciar um bloco de notas do Apache Zeppelin

1. Do cluster do Spark **descrição geral**, selecione **bloco de notas do Zeppelin** partir **dashboards de clusters**. Introduza as credenciais de administrador para o cluster.  

   > [!NOTE]  
   > Também pode aceder ao bloco de notas do Zeppelin para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de notas. O painel do cabeçalho, navegue para **Notebook** > **criar nova nota**.

    ![Criar um novo bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "criar um novo bloco de notas do Zeppelin")

    Introduza um nome para o bloco de notas, em seguida, selecione **criar nota**.

3. Certifique-se de que o cabeçalho de bloco de notas mostra um estado ligado. Ele está em falta por um ponto verde no canto superior direito.

    ![Estado de bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "estado de bloco de notas do Zeppelin")

4. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster do Spark no HDInsight, o ficheiro de dados de exemplo `hvac.csv`, é copiado para a conta do storage associada em `\HdiSamples\SensorSampleData\hvac`.

    No parágrafo vazio que é criado por predefinição no novo bloco de notas, cole o fragmento seguinte.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Prima **SHIFT + ENTER** ou clique nas **reproduzir** botão para o parágrafo executar o trecho de código. O estado no canto direito do parágrafo deve progride de PREPARADO, pendente, em execução para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

    ![Criar uma tabela temporária de dados não processados](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "criar uma tabela temporária de dados não processados")

    Também pode fornecer um título para cada parágrafo. No canto direito do parágrafo, selecione o **configurações** ícone (sprocket) e, em seguida, selecione **Mostrar título**.  

    > [!NOTE]  
    > % spark2 interpretador não é suportado em blocos de notas do Zeppelin em todas as versões do HDInsight e % sh interpretador não será suportado do HDInsight 4.0 e superior.

5. Agora, pode executar declarações do Spark SQL `hvac` tabela. Cole a seguinte consulta um novo parágrafo. A consulta obtém o ID de construção e a diferença entre o destino e as temperaturas reais para cada criar numa determinada data. Prima **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    O **% sql** o bloco de notas para utilizar o interpretador de Livy Scala informa a instrução no início.

6. Selecione o **gráfico de barras** ícone para alterar a apresentação.  **as definições**, que aparece depois de selecionar **gráfico de barras**, permite-lhe escolher **chaves**, e **valores**.  Captura de ecrã seguinte mostra a saída.

    ![Execute uma instrução de SQL do Spark com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "execute uma instrução de SQL do Spark com o bloco de notas")

7. Também pode executar declarações do Spark SQL a utilização de variáveis na consulta. O fragmento seguinte mostra como definir uma variável, `Temp`, na consulta com os valores possíveis que pretende consultar com. Ao executar a consulta em primeiro lugar, um lista pendente é preenchido automaticamente com os valores que especificou para a variável.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Cole este trecho de código num novo parágrafo e prima **SHIFT + ENTER**. Em seguida, selecione **65** partir a **Temp** pendente ist. 

8. Selecione o **gráfico de barras** ícone para alterar a apresentação.  Em seguida, selecione **definições** e efetue as seguintes alterações:

    * **Grupos:**  Adicione **targettemp**.  
    * **Valores:** 1. Remova **data**.  2. Adicione **temp_diff**.  3.  Alterar o agregador partir **soma** ao **média**.  

    Captura de ecrã seguinte mostra a saída.

    ![Execute uma instrução de SQL do Spark com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "execute uma instrução de SQL do Spark com o bloco de notas")

9. Reinicie o interpretador de Livy para sair do aplicativo. Para tal, abra as definições do interpretador ao selecionar o com sessão iniciada no nome de utilizador no canto superior direito e, em seguida, selecione **interpretador**.  

    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")

10. Desloque-se para **livy**e, em seguida, selecione **reiniciar**.  Selecione **OK** na linha de comandos.

    ![Reinicie o intepreter Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar intepreter o Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como posso utilizar pacotes externos com o bloco de notas?
Pode configurar o bloco de notas do Zeppelin no cluster do Apache Spark no HDInsight para utilizar pacotes externos, fornecidas pela Comunidade, que não estão incluído-de-pronta no cluster. Pode pesquisar o [repositório Maven](https://search.maven.org/) para obter a lista completa de pacotes que estão disponíveis. Também pode obter uma lista dos pacotes disponíveis a partir de outras origens. Por exemplo, está disponível numa lista completa dos pacotes fornecidos pela Comunidade [pacotes de Spark](https://spark-packages.org/).

Neste artigo, verá como utilizar o [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote com o bloco de notas do Jupyter.

1. Abra as definições do interpretador. No canto superior direito, selecione conectado no nome de utilizador, em seguida, selecione **interpretador**.

    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")

2. Desloque-se para **livy**, em seguida, selecione **editar**.

    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "alterar as definições do interpretador")

3. Adicione uma nova chave denominada `livy.spark.jars.packages`e defina seu valor no formato `group:id:version`. Deste modo, se pretender utilizar o [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote, tem de definir o valor da chave a `com.databricks:spark-csv_2.10:1.4.0`.

    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "alterar as definições do interpretador")

    Selecione **guardar** e, em seguida, reinicie o interpretador de Livy.

4. Se quiser compreender como deparar-se com o valor da chave introduzido acima, aqui está como.
   
    a. Localize o pacote no repositório Maven. Para este tutorial, utilizamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A partir do repositório, reúna os valores para **GroupId**, **ArtifactId**, e **versão**.
   
    ![Utilizar pacotes externos com o bloco de notas do Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "utilizar pacotes externos com o bloco de notas do Jupyter")
   
    c. Concatenar os três valores separados por vírgula (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde são guardados os blocos de notas do Zeppelin?
Os blocos de notas do Zeppelin são guardados para os principais do cluster. Então, se eliminar o cluster, os blocos de notas serão também eliminados. Se quiser preservar seus blocos de notas para utilização posterior no outros clusters, tem de exportá-las depois de concluir as tarefas em execução. Para exportar um bloco de notas, selecione o **exportar** ícone, conforme mostrado na imagem abaixo.

![Baixe o bloco de notas](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "transferir o bloco de notas")

Esta ação guarda o bloco de notas como um ficheiro JSON na sua localização de transferência.

## <a name="livy-session-management"></a>Gerenciamento de sessões do Livy
Quando executa o primeiro parágrafo de código no seu bloco de notas do Zeppelin, é criada uma nova sessão do Livy no seu cluster do HDInsight Spark. Esta sessão é compartilhada entre todos os blocos de notas do Zeppelin que, em seguida, criar. Se por algum motivo o Livy sessão é eliminado (reinício de cluster, etc.), não será capaz de executar tarefas do bloco de notas do Zeppelin.

Nesse caso, tem de efetuar os seguintes passos antes de começar executando tarefas a partir de um bloco de notas do Zeppelin.  

1. Reinicie o interpretador de Livy do bloco de notas do Zeppelin. Para tal, abra as definições do interpretador ao selecionar o com sessão iniciada no nome de utilizador no canto superior direito, em seguida, selecione **interpretador**.

    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")

2. Desloque-se para **livy**, em seguida, selecione **reiniciar**.

    ![Reinicie o intepreter Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar intepreter o Zeppelin")

3. Execute uma célula de código a partir de um bloco de notas do Zeppelin existente. Esta ação cria uma nova sessão do Livy do cluster do HDInsight.

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
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o plug-in ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
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
