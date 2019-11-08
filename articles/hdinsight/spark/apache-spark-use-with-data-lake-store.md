---
title: Analisar Azure Data Lake Storage Gen1 com o HDInsight Apache Spark
description: Executar trabalhos de Apache Spark para analisar os dados armazenados no Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818172"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Usar o cluster HDInsight Spark para analisar dados no Data Lake Storage Gen1

Neste artigo, você usa [Jupyter Notebook](https://jupyter.org/) disponíveis com clusters do HDInsight Spark para executar um trabalho que lê dados de uma conta de data Lake Storage.

## <a name="prerequisites"></a>Pré-requisitos

* Azure Data Lake Storage Gen1 conta. Siga as instruções em Introdução ao [Azure data Lake Storage Gen1 usando o portal do Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark cluster com Data Lake Storage Gen1 como armazenamento. Siga as instruções em [início rápido: configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]  
> Você não precisará executar esta etapa se tiver criado o cluster HDInsight com Data Lake Storage como armazenamento padrão. O processo de criação de cluster adiciona alguns dados de exemplo na conta de Data Lake Storage que você especifica ao criar o cluster. Pule para a seção usar o cluster HDInsight Spark com Data Lake Storage.

Se você criou um cluster HDInsight com Data Lake Storage como armazenamento adicional e Azure Storage Blob como armazenamento padrão, primeiro copie em alguns dados de exemplo para a conta de Data Lake Storage. Você pode usar os dados de exemplo do Azure Storage Blob associado ao cluster HDInsight. Você pode usar a [ferramenta ADLCopy](https://www.microsoft.com/download/details.aspx?id=50358) para fazer isso. Baixe e instale a ferramenta por meio do link.

1. Abra um prompt de comando e navegue até o diretório em que o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contêiner de origem para Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copie o arquivo de dados de exemplo **HVAC. csv** em **/HdiSamples/HdiSamples/SensorSampleData/HVAC/** para a conta Azure data Lake Storage. O trecho de código deve ser semelhante a:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Verifique se os nomes de arquivo e caminho usam a capitalização correta.

3. Você será solicitado a inserir as credenciais para a assinatura do Azure sob a qual você tem sua conta de Data Lake Storage. Verá um resultado semelhante ao seguinte fragmento:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    O arquivo de dados (**HVAC. csv**) será copiado em uma pasta **/hvac** na conta data Lake Storage.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Usar um cluster HDInsight Spark com Data Lake Storage Gen1

1. No [portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster apache Spark (se você o tiver fixado no quadro inicial). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]  
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo notebook Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de notas do Jupyter")

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     ![Status de um trabalho de bloco de anotações Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Estado de uma tarefa do bloco de notas do Jupyter")

5. Carregue dados de exemplo em uma tabela temporária usando o arquivo **HVAC. csv** que você copiou para a conta de data Lake Storage Gen1. Você pode acessar os dados na conta de Data Lake Storage usando o padrão de URL a seguir.

   * Se você tiver Data Lake Storage Gen1 como armazenamento padrão, o HVAC. csv estará no caminho semelhante à seguinte URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Ou você também pode usar um formato abreviado, como o seguinte:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Se você tiver Data Lake Storage como armazenamento adicional, o HVAC. csv estará no local onde você o copiou, como:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Em uma célula vazia, Cole o exemplo de código a seguir, substitua **MYDATALAKESTORE** por seu nome de conta data Lake Storage e pressione **Shift + Enter**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

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

6. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre a mágica `%%sql`, bem como outras mágicas disponíveis com o kernel PySpark, confira [kernels disponíveis em notebooks Jupyter com clusters HDInsight Apache Spark](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

      ![Saída da tabela do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Saída do tabela do resultado da consulta")

     Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

     ![Gráfico de área do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Gráfico de área do resultado da consulta")

8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.


## <a name="next-steps"></a>Passos seguintes

* [Criar um aplicativo escalar autônomo para ser executado no cluster Apache Spark](apache-spark-create-standalone-application.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para o cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark para o cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
