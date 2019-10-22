---
title: Usar blocos de anotações do Zeppelin com cluster Apache Spark no Azure HDInsight
description: Instruções passo a passo sobre como usar notebooks Zeppelin com clusters Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 26634e2fe23e0a23540638c4559af6e11eccbe72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71180743"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Usar notebooks do Apache Zeppelin com cluster Apache Spark no Azure HDInsight

Os clusters do HDInsight Spark incluem notebooks do [Apache Zeppelin](https://zeppelin.apache.org/) que você pode usar para executar trabalhos do [Apache Spark](https://spark.apache.org/) . Neste artigo, você aprenderá a usar o notebook Zeppelin em um cluster HDInsight.

**Pré-requisitos:**

* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* O esquema de URI para o armazenamento primário de clusters. Isso seria `wasb://` para o armazenamento de BLOBs do Azure, `abfs://` para Azure Data Lake Storage Gen2 ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento de BLOB, o URI será `wasbs://`.  Consulte também [exigir transferência segura no armazenamento do Azure](../../storage/common/storage-require-secure-transfer.md) para obter mais informações.

## <a name="launch-an-apache-zeppelin-notebook"></a>Iniciar um notebook Apache Zeppelin

1. Na **visão geral**do cluster do Spark, selecione **Zeppelin Notebook** em **painéis do cluster**. Insira as credenciais de administrador para o cluster.  

   > [!NOTE]  
   > Você também pode acessar o notebook Zeppelin para o cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de notas. No painel de cabeçalho, navegue até o **bloco de anotações**  > **criar nova anotação**.

    ![Criar um novo notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Criar um novo notebook Zeppelin")

    Insira um nome para o bloco de anotações e, em seguida, selecione **criar anotação**.

3. Verifique se o cabeçalho do bloco de anotações mostra um status conectado. Ele é indicado por um ponto verde no canto superior direito.

    ![Status do Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status do Zeppelin Notebook")

4. Carregue dados de exemplo para uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, `hvac.csv`, é copiado para a conta de armazenamento associada em `\HdiSamples\SensorSampleData\hvac`.

    No parágrafo vazio que é criado por padrão no novo bloco de anotações, Cole o trecho a seguir.

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

    Pressione **Shift + Enter** ou clique no botão **reproduzir** para o parágrafo executar o trecho de código. O status no canto direito do parágrafo deve progredir de pronto, pendente, em execução para concluído. A saída aparece na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

    ![Criar uma tabela temporária com base em dados brutos](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Criar uma tabela temporária com base em dados brutos")

    Você também pode fornecer um título para cada parágrafo. No canto direito do parágrafo, selecione o ícone de **configurações** (Sprocket) e, em seguida, selecione **Mostrar título**.  

    > [!NOTE]  
    > % spark2 interpretador não tem suporte em notebooks Zeppelin em todas as versões do HDInsight e o intérprete% sh não terá suporte do HDInsight 4,0 em diante.

5. Agora você pode executar instruções SQL do Spark na tabela `hvac`. Cole a consulta a seguir em um novo parágrafo. A consulta recupera a ID de edifício e a diferença entre as temperaturas de destino e reais para cada edifício em uma determinada data. Pressione **Shift + Enter**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    A instrução **% SQL** no início informa ao bloco de anotações para usar o interpretador Livy escalar.

6. Selecione o ícone de **gráfico de barras** para alterar a exibição.  **as configurações**, que aparecem depois que você selecionou **gráfico de barras**, permitem que você escolha **chaves**e **valores**.  A captura de tela a seguir mostra a saída.

    ![Executar uma instrução SQL do Spark usando o notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Executar uma instrução SQL do Spark usando o notebook1")

7. Você também pode executar instruções SQL do Spark usando variáveis na consulta. O próximo trecho mostra como definir uma variável, `Temp`, na consulta com os possíveis valores com os quais você deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Cole este trecho de código em um novo parágrafo e pressione **Shift + Enter**. Em seguida, selecione **65** na lista suspensa **Temp** . 

8. Selecione o ícone de **gráfico de barras** para alterar a exibição.  Em seguida, selecione **configurações** e faça as seguintes alterações:

   * **Grupos:**  Adicione **targettemp**.  
   * **Valores:** uma. Remover **Data**.  2. Adicione **temp_diff**.  3.  Altere o agregador de **sum** para **AVG**.  

     A captura de tela a seguir mostra a saída.

     ![Executar uma instrução SQL do Spark usando o notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Executar uma instrução SQL do Spark usando o notebook2")

9. Reinicie o intérprete Livy para sair do aplicativo. Para fazer isso, abra as configurações do intérprete selecionando o nome de usuário conectado no canto superior direito e, em seguida, selecione **intérprete**.  

    ![Iniciar interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do hive")

10. Role até **Livy**e selecione **reiniciar**.  Selecione **OK** no prompt.

    ![Reiniciar o intérprete Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reiniciar o intérprete Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como fazer usar pacotes externos com o bloco de anotações?
Você pode configurar o notebook Zeppelin no cluster Apache Spark no HDInsight para usar pacotes externos, contribuídos pela Comunidade, que não estão incluídos prontos para uso no cluster. Você pode pesquisar o [repositório do Maven](https://search.maven.org/) para obter a lista completa de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes contribuídos pela Comunidade está disponível em [pacotes do Spark](https://spark-packages.org/).

Neste artigo, você verá como usar o pacote [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o notebook Jupyter.

1. Abra as configurações do intérprete. No canto superior direito, selecione o nome de usuário conectado e, em seguida, selecione **intérprete**.

    ![Iniciar interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do hive")

2. Role até **Livy**e, em seguida, selecione **Editar**.

    ![Alterar dispositivo1 do intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar dispositivo1 do intérprete")

3. Adicione uma nova chave chamada `livy.spark.jars.packages` e defina seu valor no formato `group:id:version`. Portanto, se você quiser usar o pacote [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , deverá definir o valor da chave para `com.databricks:spark-csv_2.10:1.4.0`.

    ![Alterar settings2 do intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar settings2 do intérprete")

    Selecione **salvar** e reinicie o intérprete Livy.

4. Se você quiser entender como chegar ao valor da chave digitada acima, veja como.
   
    a. Localize o pacote no repositório do Maven. Para este artigo, usamos o [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. No repositório, reúna os valores para **GroupId**, **artefatoid**e **versão**.
   
    ![Usar pacotes externos com o Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Usar pacotes externos com o Jupyter Notebook")
   
    c. Concatene os três valores, separados por dois-pontos ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde os blocos de anotações do Zeppelin são salvos?
Os notebooks Zeppelin são salvos no cluster cabeçalho. Portanto, se você excluir o cluster, os blocos de anotações também serão excluídos. Se desejar preservar seus blocos de anotações para uso posterior em outros clusters, você deverá exportá-los depois de concluir a execução dos trabalhos. Para exportar um bloco de anotações, selecione o ícone **Exportar** , conforme mostrado na imagem abaixo.

![Baixar bloco de anotações](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Baixar o bloco de anotações")

Isso salva o bloco de anotações como um arquivo JSON em seu local de download.

## <a name="livy-session-management"></a>Gerenciamento de sessão Livy
Quando você executa o primeiro parágrafo de código em seu notebook Zeppelin, uma nova sessão Livy é criada em seu cluster HDInsight Spark. Esta sessão é compartilhada entre todos os blocos de anotações do Zeppelin que você cria posteriormente. Se, por algum motivo, a sessão Livy for eliminada (reinicialização do cluster, etc.), você não poderá executar trabalhos do notebook Zeppelin.

Nesse caso, você deve executar as etapas a seguir antes de poder iniciar a execução de trabalhos de um notebook Zeppelin.  

1. Reinicie o intérprete Livy do notebook Zeppelin. Para fazer isso, abra as configurações do intérprete selecionando o nome de usuário conectado no canto superior direito e, em seguida, selecione **intérprete**.

    ![Iniciar interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do hive")

2. Role até **Livy**e selecione **reiniciar**.

    ![Reiniciar o intérprete Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reiniciar o intérprete Zeppelin")

3. Executar uma célula de código de um notebook Zeppelin existente. Isso cria uma nova sessão Livy no cluster HDInsight.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para criar e enviar Apache Spark aplicativos escalares](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de ferramentas do HDInsight para IntelliJ IDEA para depurar aplicativos Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
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
