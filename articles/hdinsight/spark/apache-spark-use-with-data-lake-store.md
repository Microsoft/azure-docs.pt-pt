---
title: Analise Azure Data Lake Storage Gen1 com HDInsight Apache Spark
description: Executar empregos apache spark para analisar dados armazenados em Azure Data Lake Storage Gen1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: e79c2f361108f1daa3c4a125491d1b399e050648
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863705"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Use o cluster HDInsight Spark para analisar dados na Data Lake Storage Gen1

Neste artigo, você usa [o Jupyter Notebook](https://jupyter.org/) disponível com clusters HDInsight Spark para executar um trabalho que lê dados de uma conta de Armazenamento de Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

* Conta Azure Data Lake Storage Gen1. Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Aglomerado de faíscas Azure HDInsight com data lake storage gen1 como armazenamento. Siga as instruções no [Quickstart: Instale clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]  
> Não precisa de executar este passo se criou o cluster HDInsight com o Data Lake Storage como armazenamento predefinido. O processo de criação de cluster adiciona alguns dados de amostra na conta de Armazenamento do Lago de Dados que especifica ao criar o cluster. Salte para a secção Use hdInsight Spark cluster com data lake storage.

Se criou um cluster HDInsight com o Data Lake Storage como armazenamento adicional e a Mancha de Armazenamento Azure como armazenamento predefinido, deve primeiro copiar alguns dados da amostra para a conta de Armazenamento do Lago de Dados. Pode utilizar os dados da amostra a partir da Bolha de Armazenamento Azure associada ao cluster HDInsight. Pode utilizar a [ferramenta ADLCopy](https://www.microsoft.com/download/details.aspx?id=50358) para o fazer. Faça o download e instale a ferramenta a partir do link.

1. Abra uma solicitação de comando e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy` .

2. Executar o seguinte comando para copiar uma bolha específica do recipiente de origem para o armazenamento do Data Lake:

    ```scala
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Copie o ficheiro de dados da amostra **HVAC.csv** em **/HdiSamples/HdiSamples/SensorSampleData/avac/** para a conta de armazenamento do Lago de Dados Azure. O código deve parecer:

    ```scala
    AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

   > [!WARNING]  
   > Certifique-se de que os nomes do ficheiro e do caminho utilizam a capitalização adequada.

3. É-lhe pedido que introduza as credenciais para a subscrição do Azure sob a qual tem a sua conta de Armazenamento de Data Lake. Verá um resultado semelhante ao seguinte fragmento:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Copy Completed. 1 file copied.
    ```

    O ficheiro de dados **(HVAC.csv)** será copiado sob uma pasta **/hvac** na conta de Armazenamento do Lago de Dados.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Use um cluster HDInsight Spark com data lake storage gen1

1. A partir do [portal Azure](https://portal.azure.com/), a partir da placa de partida, clique no azulejo para o seu cluster Apache Spark (se o fixar na placa de partida). Também pode navegar para o seu cluster em **Browse All**  >  **HDInsight Clusters**.

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]  
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    :::image type="content" source="./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png " alt-text="Criar um novo Caderno Jupyter" border="true":::

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

    ```scala
    from pyspark.sql.types import *
    ```

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     :::image type="content" source="./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png " alt-text="Estatuto de um trabalho de Caderno Jupyter" border="true":::

5. Carregue os dados da amostra numa tabela temporária utilizando o ficheiro **HVAC.csv** que copiou para a conta da Data Lake Storage Gen1. Pode aceder aos dados na conta de Armazenamento do Lago de Dados utilizando o seguinte padrão URL.

   * Se tiver a Data Lake Storage Gen1 como armazenamento predefinido, HVAC.csv estará no caminho semelhante ao seguinte URL:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

       Ou, também pode utilizar um formato encurtado, como:

        ```scala
        adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv
        ```

   * Se tiver o Armazenamento do Lago de Dados como armazenamento adicional, HVAC.csv estará no local onde o copiou, tais como:

        ```scala
        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>
        ```

     Numa célula vazia, cole o seguinte exemplo de código, substitua **MYDATALAKESTORE** pelo nome da sua conta de armazenamento de data lake, e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

      ```scala
      # Load the data. The path below assumes Data Lake Storage is   default storage for the Spark cluster
      hvacText = sc.textFile("adl://MYDATALAKESTORazuredatalakestore.  net/cluster/mysparkclusteHdiSamples/HdiSamples/  SensorSampleData/hvac/HVAC.csv")

      # Create the schema
      hvacSchema = StructType([StructField("date", StringTy(), False)  ,StructField("time", StringType(), FalseStructField  ("targettemp", IntegerType(), FalseStructField("actualtemp",   IntegerType(), FalseStructField("buildingID", StringType(),   False)])

      # Parse the data in hvacText
      hvac = hvacText.map(lambda s: s.split(",")).filt(lambda s: s  [0] != "Date").map(lambda s:(str(s[0]), s(s[1]), int(s[2]), int  (s[3]), str(s[6]) ))

      # Create a data frame
      hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

      # Register the data fram as a table to run queries against
      hvacdf.registerTempTable("hvac")
      ```

6. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre a `%%sql` magia, bem como outras magias disponíveis com o kernel PySpark, consulte [Kernels disponível em Cadernos Jupyter com aglomerados Apache Spark HDInsight.](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
7. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

      :::image type="content" source="./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png " alt-text="Saída do tabela do resultado da consulta" border="true":::

     Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

     :::image type="content" source="./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png " alt-text="Gráfico de área do resultado da consulta" border="true":::

8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.


## <a name="next-steps"></a>Passos seguintes

* [Crie uma aplicação Scala autónoma para executar no cluster Apache Spark](apache-spark-create-standalone-application.md)
* [Utilize ferramentas HDInsight em Azure Toolkit para IntelliJ para criar aplicações Apache Spark para cluster HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Utilize ferramentas HDInsight em Azure Toolkit para eclipse para criar aplicações Apache Spark para cluster HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)