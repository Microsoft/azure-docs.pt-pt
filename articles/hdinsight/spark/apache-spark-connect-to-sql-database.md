---
title: Use o Apache Spark para ler e escrever dados para a Base de Dados Azure SQL
description: Saiba como configurar uma ligação entre o cluster HDInsight Spark e a Base de Dados Azure SQL. Para ler dados, escrever dados e transmitir dados numa base de dados SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d979a68f4e3aa0071fb7654647610af1fbf95e90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023981"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Utilize o cluster HDInsight Spark para ler e escrever dados para a Base de Dados Azure SQL

Saiba como ligar um cluster Apache Spark em Azure HDInsight com a Base de Dados Azure SQL. Em seguida, leia, escreva e transmita dados para a base de dados SQL. As instruções deste artigo usam um Caderno Jupyter para executar os cortes de código Scala. No entanto, pode criar uma aplicação autónoma em Scala ou Python e fazer as mesmas tarefas.

## <a name="prerequisites"></a>Pré-requisitos

* Aglomerado de faíscas Azure HDInsight.  Siga as instruções da [Create a Apache Spark cluster em HDInsight](apache-spark-jupyter-spark-sql.md).

* Base de Dados SQL do Azure. Siga as instruções na [Criar uma base de dados na Base de Dados Azure SQL](../../azure-sql/database/single-database-create-quickstart.md). Certifique-se de criar uma base de dados com a amostra de esquema e dados **AdventureWorksLT.** Além disso, certifique-se de criar uma regra de firewall ao nível do servidor para permitir que o endereço IP do seu cliente aceda à base de dados SQL. As instruções para adicionar a regra de firewall estão disponíveis no mesmo artigo. Depois de criar a sua base de dados SQL, certifique-se de que mantém à mão os seguintes valores. Precisa deles para ligar à base de dados de um aglomerado de faíscas.

    * Nome do servidor.
    * Nome da base de dados.
    * Azure SQL Base de Dados nome de utilizador /palavra-passe.

* SQL Server Management Studio (SSMS). Siga as instruções da [Utilização SSMS para ligar e consultar dados](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criar um Bloco de Notas do Jupyter Notebook

Comece por criar um Caderno Jupyter associado ao cluster Spark. Usa este caderno para executar os códigos usados neste artigo.

1. A partir do [portal Azure,](https://portal.azure.com/)abra o seu aglomerado.
1. Selecione **o caderno Jupyter** por baixo **dos tabliers cluster** no lado direito.  Se não vir **os dashboards do Cluster,** selecione **Overview** no menu esquerdo. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Caderno jupyter em Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Caderno jupyter em Faísca")

   > [!NOTE]  
   > Também pode aceder ao portátil Jupyter no cluster Spark, abrindo o seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. No caderno Jupyter, a partir do canto superior direito, clique em **New** e, em seguida, clique em **Spark** para criar um caderno Scala. Os cadernos Jupyter no cluster HDInsight Spark também fornecem o kernel **PySpark** para aplicações Python2, e o kernel **PySpark3** para aplicações Python3. Para este artigo, criamos um caderno Scala.

    ![Kernels para o caderno jupyter em Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels para o caderno jupyter em Spark")

    Para obter mais informações sobre os kernels, veja [Utilizar kernels de blocos de notas do Jupyter com clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Neste artigo, usamos um núcleo spark (Scala) porque os dados de streaming da Spark para a Base de Dados SQL só são suportados em Scala e Java atualmente. Mesmo que ler e escrever em SQL possa ser feito usando Python, para consistência neste artigo, usamos Scala para as três operações.

1. Um novo caderno abre com um nome padrão, **Sem Título**. Clique no nome do caderno e insira um nome à sua escolha.

    ![Fornecer um nome para o bloco de notas](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Fornecer um nome para o bloco de notas")

Pode agora começar a criar a sua aplicação.

## <a name="read-data-from-azure-sql-database"></a>Ler dados da Base de Dados Azure SQL

Nesta secção, você lê dados de uma tabela (por exemplo, **SalesLT.Address)** que existe na base de dados AdventureWorks.

1. Num novo caderno Jupyter, numa célula de código, cole o seguinte corte e substitua os valores do espaço reservado pelos valores da sua base de dados.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. Use o snippet abaixo para construir um URL JDBC que pode passar para as APIs do dataframe spark. O código cria um `Properties` objeto para manter os parâmetros. Cole o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Utilize o snippet abaixo para criar um dataframe com os dados de uma tabela na sua base de dados. Neste snippet, usamos uma `SalesLT.Address` tabela que está disponível como parte da base de **dados AdventureWorksLT.** Cole o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Agora pode fazer operações no dataframe, como obter o esquema de dados:

    ```scala
    sqlTableDF.printSchema
    ```

    Vê-se uma saída semelhante à seguinte imagem:

    ![saída de schema](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "saída de schema")

1. Também pode fazer operações como, recuperar as 10 melhores filas.

    ```scala
    sqlTableDF.show(10)
    ```

1. Ou, recuperar colunas específicas do conjunto de dados.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Escreva dados na Base de Dados Azure SQL

Nesta secção, utilizamos um ficheiro CSV de amostra disponível no cluster para criar uma tabela na sua base de dados e povoá-la com dados. O ficheiro CSV da amostra **(HVAC.csv)** está disponível em todos os clusters HDInsight em `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` .

1. Num novo caderno Jupyter, numa célula de código, cole o seguinte corte e substitua os valores do espaço reservado pelos valores da sua base de dados.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. O seguinte snippet constrói um URL JDBC que pode passar para as APIs do dataframe spark. O código cria um `Properties` objeto para manter os parâmetros. Cole o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Utilize o seguinte corte para extrair o esquema dos dados em HVAC.csv e utilizar o esquema para carregar os dados do CSV num dataframe, `readDf` . Cole o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Utilize o `readDf` quadro de dados para criar uma tabela temporária, `temphvactable` . Em seguida, use a mesa temporária para criar uma mesa de colmeia, `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Finalmente, use a mesa da colmeia para criar uma tabela na sua base de dados. O seguinte corte `hvactable` cria-se na Base de Dados Azure SQL.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Ligue-se à Base de Dados Azure SQL utilizando SSMS e verifique se vê uma `dbo.hvactable` ali.

    a. Inicie sSMS e ligue-se à Base de Dados Azure SQL fornecendo detalhes de conexão como mostrado na imagem abaixo.

    ![Ligue à Base de Dados SQL utilizando sSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Ligue à Base de Dados SQL utilizando sSMS1")

    b. A partir do **Object Explorer,** expanda a base de dados e o nó de tabela para ver o **dbo.hvactable** criado.

    ![Ligue à Base de Dados SQL utilizando sSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Ligue à Base de Dados SQL utilizando sSMS2")

1. Executar uma consulta em SSMS para ver as colunas na tabela.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Transmita os dados para a Base de Dados Azure SQL

Nesta secção, transmitimos dados para o `hvactable` que criou na secção anterior.

1. Como primeiro passo, certifique-se de que não há registos no `hvactable` . Utilizando SSMS, corram a seguinte consulta sobre a tabela.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Crie um novo caderno Jupyter no cluster HDInsight Spark. Numa célula de código, cole o seguinte corte e, em seguida, prima **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Transmitimos dados do **HVAC.csv** para o `hvactable` . HVAC.csv ficheiro está disponível no cluster em `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . No seguinte corte, primeiro obtemos o esquema dos dados a ser transmitido. Depois, criamos um dataframe de streaming usando esse esquema. Cole o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. A saída mostra o esquema de **HVAC.csv**. O `hvactable` tem o mesmo esquema também. A saída lista as colunas na tabela.

    !['hdinsight Apache Spark schema table'](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Esquema da mesa")

1. Por fim, utilize o seguinte corte para ler os dados da HVAC.csv e transmita-os para a `hvactable` sua base de dados. Cole o corte numa célula de código, substitua os valores do espaço reservado pelos valores da sua base de dados e, em seguida, prima **SHIFT + ENTER** para executar.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. Verifique se os dados estão a ser transmitidos para o `hvactable` seguinte, executando a seguinte consulta no SQL Server Management Studio (SSMS). Cada vez que faz a consulta, mostra o número de filas na mesa a aumentar.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Passos seguintes

* [Use o cluster HDInsight Spark para analisar dados no armazenamento do Data Lake](apache-spark-use-with-data-lake-store.md)
* [Carregue os dados e execute consultas num cluster Apache Spark em Azure HDInsight](apache-spark-load-data-run-query.md)
* [Use o Fluxo Estruturado de Faíscas Apache com Apache Kafka em HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
