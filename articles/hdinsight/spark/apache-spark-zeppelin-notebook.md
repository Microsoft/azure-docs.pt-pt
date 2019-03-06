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
ms.date: 02/21/2018
ms.openlocfilehash: 600b2796f1cca5d6ac2c478a5eb30e12f30b670a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449755"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utilizar blocos de notas do Zeppelin do Apache com o cluster do Apache Spark no Azure HDInsight

Os clusters do Spark do HDInsight incluem [Apache Zeppelin](https://zeppelin.apache.org/) blocos de notas que pode utilizar para executar [Apache Spark](https://spark.apache.org/) tarefas. Neste artigo, irá aprender a utilizar o bloco de notas do Zeppelin num cluster do HDInsight.

**Pré-requisitos:**

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-an-apache-zeppelin-notebook"></a>Iniciar um bloco de notas do Apache Zeppelin
1. No painel do cluster do Spark, clique em **Dashboard de clusters**e, em seguida, clique em **bloco de notas do Zeppelin**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.
   
   > [!NOTE]  
   > Também pode aceder ao bloco de notas do Zeppelin para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

1. Crie um novo bloco de notas. No painel do cabeçalho, clique em **Notebook**e, em seguida, clique em **criar nova nota**.
   
    ![Criar um novo bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "criar um novo bloco de notas do Zeppelin")
   
    Introduza um nome para o bloco de notas e, em seguida, clique em **criar nota**.
1. Além disso, certifique-se de que o cabeçalho de bloco de notas mostra um estado ligado. Ele está em falta por um ponto verde no canto superior direito.
   
    ![Estado de bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "estado de bloco de notas do Zeppelin")
1. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster do Spark no HDInsight, o ficheiro de dados de exemplo **Hvac**, é copiado para a conta de armazenamento associado sob **\HdiSamples\SensorSampleData\hvac**.
   
    No parágrafo vazio que é criado por predefinição no novo bloco de notas, cole o fragmento seguinte.
   
        %livy2.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
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
   
    Prima **SHIFT + ENTER** ou clique nas **reproduzir** botão para o parágrafo executar o trecho de código. O estado no canto direito do parágrafo deve progride de PREPARADO, pendente, em execução para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:
   
    ![Criar uma tabela temporária de dados não processados](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "criar uma tabela temporária de dados não processados")
   
    Também pode fornecer um título para cada parágrafo. No canto direito, clique nas **definições** ícone e clique em **Mostrar título**.

> [!NOTE]  
> % spark2 interpretador não é suportado em blocos de notas do Zeppelin em todas as versões do HDInsight e % sh interpretador não será suportado do HDInsight 4.0 e superior.

1. Agora, pode executar declarações do Spark SQL **hvac** tabela. Cole a seguinte consulta um novo parágrafo. A consulta obtém o ID de construção e a diferença entre o destino e as temperaturas reais para cada criar numa determinada data. Prima **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    O **% sql** o bloco de notas para utilizar o interpretador de Livy Scala informa a instrução no início.
   
    Captura de ecrã seguinte mostra a saída.
   
    ![Execute uma instrução de SQL do Spark com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "execute uma instrução de SQL do Spark com o bloco de notas")
   
     Clique em Opções de apresentação (realçadas no retângulo) para alternar entre as representações diferentes para o mesmo resultado. Clique em **definições** escolher quais consitutes a chave e os valores na saída. Captura de ecrã acima utiliza **buildingID** como a chave e a média dos **temp_diff** como o valor.
1. Também pode executar declarações do Spark SQL a utilização de variáveis na consulta. O fragmento seguinte mostra como definir uma variável **Temp**, na consulta com os valores possíveis que pretende consultar com. Ao executar a consulta em primeiro lugar, um lista pendente é preenchido automaticamente com os valores que especificou para a variável.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Cole este trecho de código num novo parágrafo e prima **SHIFT + ENTER**. Captura de ecrã seguinte mostra a saída.
   
    ![Execute uma instrução de SQL do Spark com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "execute uma instrução de SQL do Spark com o bloco de notas")
   
    Para consultas subsequentes, pode selecionar um novo valor na lista suspensa e executar novamente a consulta. Clique em **definições** escolher quais consitutes a chave e os valores na saída. Captura de ecrã acima utiliza **buildingID** como a chave, a média dos **temp_diff** como o valor, e **targettemp** como o grupo.
1. Reinicie o interpretador de Livy para sair do aplicativo. Para tal, abra as definições do interpretador ao clicar em conectado no nome de utilizador no canto superior direito e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
1. Desloque-se às definições do interpretador de Livy e, em seguida, clique em **reiniciar**.
   
    ![Reinicie o interpretador de Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar o interpretador de Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como posso utilizar pacotes externos com o bloco de notas?
Pode configurar o bloco de notas do Zeppelin no cluster do Apache Spark no HDInsight (Linux) para utilizar pacotes externos, fornecidas pela Comunidade, que não estão incluído-de-pronta no cluster. Pode pesquisar o [repositório Maven](https://search.maven.org/) para obter a lista completa de pacotes que estão disponíveis. Também pode obter uma lista dos pacotes disponíveis a partir de outras origens. Por exemplo, está disponível numa lista completa dos pacotes fornecidos pela Comunidade [pacotes de Spark](https://spark-packages.org/).

Neste artigo, verá como utilizar o [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote com o bloco de notas do Jupyter.

1. Abra as definições do interpretador. No canto superior direito, clique em conectado no nome de utilizador e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
1. Desloque-se às definições do interpretador de Livy e, em seguida, clique em **editar**.
   
    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "alterar as definições do interpretador")
1. Adicione uma nova chave, chamado **livy.spark.jars.packages** e defina seu valor no formato `group:id:version`. Deste modo, se pretender utilizar o [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote, tem de definir o valor da chave a `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "alterar as definições do interpretador")
   
    Clique em **guardar** e, em seguida, reinicie o interpretador de Livy.
1. **Sugestão**: Se quiser compreender como deparar-se com o valor da chave introduzido acima, aqui está como.
   
    a. Localize o pacote no repositório Maven. Para este tutorial, utilizamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A partir do repositório, reúna os valores para **GroupId**, **ArtifactId**, e **versão**.
   
    ![Utilizar pacotes externos com o bloco de notas do Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "utilizar pacotes externos com o bloco de notas do Jupyter")
   
    c. Concatenar os três valores separados por vírgula (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde são guardados os blocos de notas do Zeppelin?
Os blocos de notas do Zeppelin são guardados para os principais do cluster. Então, se eliminar o cluster, os blocos de notas serão também eliminados. Se quiser preservar seus blocos de notas para utilização posterior no outros clusters, tem de exportá-las depois de concluir as tarefas em execução. Para exportar um bloco de notas, clique a **exportar** ícone, conforme mostrado na imagem abaixo.

![Baixe o bloco de notas](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "transferir o bloco de notas")

Esta ação guarda o bloco de notas como um ficheiro JSON na sua localização de transferência.

## <a name="livy-session-management"></a>Gerenciamento de sessões do Livy
Quando executa o primeiro parágrafo de código no seu bloco de notas do Zeppelin, é criada uma nova sessão do Livy no seu cluster do HDInsight Spark. Esta sessão é compartilhada entre todos os blocos de notas do Zeppelin que, em seguida, criar. Se por algum motivo o Livy sessão é eliminado (reinício de cluster, etc.), não será capaz de executar tarefas do bloco de notas do Zeppelin.

Nesse caso, tem de efetuar os seguintes passos antes de começar executando tarefas a partir de um bloco de notas do Zeppelin. 

1. Reinicie o interpretador de Livy do bloco de notas do Zeppelin. Para tal, abra as definições do interpretador ao clicar em conectado no nome de utilizador no canto superior direito e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
1. Desloque-se às definições do interpretador de Livy e, em seguida, clique em **reiniciar**.
   
    ![Reinicie o interpretador de Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar o interpretador de Zeppelin")
1. Execute uma célula de código a partir de um bloco de notas do Zeppelin existente. Esta ação cria uma nova sessão do Livy do cluster do HDInsight.

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







