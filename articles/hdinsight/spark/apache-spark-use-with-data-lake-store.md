---
title: Utilizar o Apache Spark para analisar dados de geração 1 de armazenamento do Azure Data Lake
description: Executar tarefas do Spark para analisar dados armazenados na geração 1 de armazenamento do Azure Data Lake
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 97a9d688eaa607df9677b6e1e2e3759cbe53bd5c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122542"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Utilizar o cluster do Spark do HDInsight para analisar dados de geração 1 de armazenamento do Data Lake

Neste tutorial, vai utilizar [bloco de notas do Jupyter](https://jupyter.org/) disponível com os clusters do Spark do HDInsight para executar uma tarefa que lê dados a partir de uma conta de armazenamento do Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

* Conta de geração 1 do Data Lake Storage do Azure. Siga as instruções em [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Como armazenamento de cluster do Azure HDInsight Spark com geração 1 de armazenamento do Data Lake. Siga as instruções em [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]  
> Não é necessário efetuar este passo se tiver criado o cluster do HDInsight com o armazenamento do Data Lake como armazenamento predefinido. O processo de criação do cluster adiciona alguns dados de exemplo na conta de armazenamento do Data Lake que especificou ao criar o cluster. Avance para o cluster de utilizar o Spark do HDInsight secção com o armazenamento do Data Lake.

Se tiver criado um cluster do HDInsight com o armazenamento do Data Lake como armazenamento adicional e o Azure Storage Blob como armazenamento predefinido, deve primeiro copie alguns dados de exemplo para a conta de armazenamento do Data Lake. Pode utilizar o exemplo de dados de Blob de armazenamento do Azure associados ao cluster de HDInsight. Pode utilizar o [ADLCopy ferramenta](https://aka.ms/downloadadlcopy) para fazer isso. Baixe e instale a ferramenta a partir da ligação.

1. Abra uma linha de comandos e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contentor de origem para o armazenamento do Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copiar o **Hvac** o ficheiro de dados de exemplo na **/HdiSamples/HdiSamples/SensorSampleData/hvac/** para a conta de armazenamento do Azure Data Lake. O trecho de código deve ser semelhante:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Certifique-se de que os nomes de ficheiro e caminho usam a capitalização adequada.

3. São-lhe pedido para introduzir as credenciais para a subscrição do Azure sob a qual tem a conta de armazenamento do Data Lake. Verá um resultado semelhante ao seguinte fragmento:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    O ficheiro de dados (**Hvac**) serão copiados numa pasta **/hvac** na conta de armazenamento do Data Lake.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Utilizar um cluster do Spark do HDInsight com Data Lake Storage Gen1

1. Partir do [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico para o seu cluster do Apache Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]  
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de notas Jupyter")

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     ![Estado de uma tarefa de bloco de notas Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Estado de uma tarefa de bloco de notas Jupyter")

5. Carregar dados de exemplo para uma tabela temporária com o **Hvac** ficheiro que copiou para a conta de geração 1 de armazenamento do Data Lake. Pode acessar os dados na conta de armazenamento do Data Lake com o padrão de URL seguinte.

   * Se tiver de geração 1 de armazenamento do Data Lake como armazenamento predefinido, Hvac estará no caminho semelhante ao seguinte URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Em alternativa, também pode usar um formato abreviado como o seguinte:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Se tiver de armazenamento do Data Lake como armazenamento adicional, Hvac estarão na localização onde copiou, tais como:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Numa célula vazia, cole o seguinte exemplo de código, substitua **MYDATALAKESTORE** com o nome da conta de armazenamento do Data Lake e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

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

6. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre o `%%sql` mágica, bem como sobre outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

      ![Saída da tabela do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Saída da tabela do resultado da consulta")

     Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

     ![Gráfico de área do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Gráfico de área do resultado da consulta")

8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.


## <a name="next-steps"></a>Passos Seguintes

* [Criar uma aplicação autónoma de Scala para ser executado no cluster do Apache Spark](apache-spark-create-standalone-application.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ para criar aplicativos de Apache Spark para o cluster do HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para criar aplicativos de Apache Spark para o cluster do HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
