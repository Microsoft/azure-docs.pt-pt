---
title: Analise o armazenamento do lago de dados Azure Gen1 com hDInsight Apache Spark
description: Executar empregos apache spark para analisar dados armazenados em Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73818172"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Use o cluster HDInsight Spark para analisar dados em Data Lake Storage Gen1

Neste artigo, você usa [O Caderno Jupyter](https://jupyter.org/) disponível com clusters HDInsight Spark para executar um trabalho que lê dados de uma conta de Armazenamento de Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

* Conta Azure Data Lake Storage Gen1. Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster de faíscas Azure HDInsight com Data Lake Storage Gen1 como armazenamento. Siga as instruções em [Quickstart: Instale agrupamentos em HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]  
> Não precisa de realizar este passo se criou o cluster HDInsight com o Armazenamento do Lago de Dados como armazenamento predefinido. O processo de criação de cluster adiciona alguns dados da amostra na conta data lake storage que especifica ao criar o cluster. Saltar para a secção Utilize o cluster HDInsight Spark com armazenamento de data lake.

Se criou um cluster HDInsight com armazenamento de data lake como armazenamento adicional e Blob de armazenamento Azure como armazenamento padrão, você deve primeiro copiar alguns dados da amostra para a conta de Armazenamento de Data Lake. Pode utilizar os dados da amostra da Blob de Armazenamento Azure associada ao cluster HDInsight. Pode utilizar a [ferramenta ADLCopy](https://www.microsoft.com/download/details.aspx?id=50358) para o fazer. Descarregue e instale a ferramenta a partir do link.

1. Abra um pedido de comando e navegue para o diretório `%HOMEPATH%\Documents\adlcopy`onde o AdlCopy é instalado, tipicamente .

2. Executar o seguinte comando para copiar uma bolha específica do recipiente de origem para data lake storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copie o ficheiro de dados da amostra **HVAC.csv** em **/HdiSamples/HdiSamples/SensorSampleData/vac/** para a conta de Armazenamento do Lago de Dados Azure. O código deve parecer:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Certifique-se de que os nomes de ficheiros e de caminhos utilizam a capitalização adequada.

3. É-lhe pedido que introduza as credenciais para a subscrição Azure sob a qual tem a sua conta data Lake Storage. Verá um resultado semelhante ao seguinte fragmento:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    O ficheiro de dados **(HVAC.csv)** será copiado sob uma pasta **/avac** na conta data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Use um cluster hDInsight Spark com Data Lake Storage Gen1

1. A partir do [portal Azure,](https://portal.azure.com/)a partir da placa de arranque, clique no azulejo para o seu cluster Apache Spark (se o fixou na placa de arranque). Também pode navegar para o seu cluster em **BrowsE All** > **HDInsight Clusters**.

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]  
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas do Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de notas do Jupyter")

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     ![Estado de uma tarefa do bloco de notas do Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Estado de uma tarefa do bloco de notas do Jupyter")

5. Carregue os dados da amostra numa tabela temporária utilizando o ficheiro **HVAC.csv** copiou para a conta Data Lake Storage Gen1. Pode aceder aos dados na conta data lake storage utilizando o seguinte padrão de URL.

   * Se tiver data Lake Storage Gen1 como armazenamento predefinido, o AVAC.csv estará no caminho semelhante ao seguinte URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Ou, também pode utilizar um formato encurtado, como o seguinte:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Se tiver o Armazenamento do Lago de Dados como armazenamento adicional, o AVAC.csv estará no local onde o copiou, como:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Numa célula vazia, cola o seguinte exemplo de código, substitua a **MYDATALAKESTORE** pelo nome da sua conta data Lake Storage e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais `%%sql` informações sobre a magia, bem como outras magias disponíveis com o kernel PySpark, consulte [kernels disponíveis em cadernos Jupyter com clusters Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

      ![Saída do tabela do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Saída do tabela do resultado da consulta")

     Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

     ![Gráfico de área do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Gráfico de área do resultado da consulta")

8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.


## <a name="next-steps"></a>Passos seguintes

* [Crie uma aplicação Scala autónoma para funcionar no cluster Apache Spark](apache-spark-create-standalone-application.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para intelliJ para criar aplicações Apache Spark para cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark para cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
