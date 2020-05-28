---
title: Use a Apache Spark para ler e escrever dados para a Base de Dados Azure SQL
description: Saiba como estabelecer uma ligação entre o cluster HDInsight Spark e uma Base de Dados Azure SQL. Para ler dados, escrever dados e transmitir dados para uma base de dados SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: f0bc1890fd5ca9c045caa6325f474e85f1b85622
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022253"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Utilize o cluster HDInsight Spark para ler e escrever dados para a Base de Dados Azure SQL

Saiba como ligar um cluster Apache Spark em Azure HDInsight a uma Base de Dados Azure SQL. Em seguida, leia, escreva e transmita dados para a base de dados SQL. As instruções deste artigo usam um Caderno Jupyter para executar os snippets do código Scala. No entanto, pode criar uma aplicação autónoma em Scala ou Python e fazer as mesmas tarefas.

## <a name="prerequisites"></a>Pré-requisitos

* Cluster de faíscas Azure HDInsight.  Siga as instruções em [Create a Apache Spark cluster in HDInsight](apache-spark-jupyter-spark-sql.md).

* Base de Dados SQL do Azure. Siga as instruções na [Create a Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Certifique-se de criar uma base de dados com o esquema e dados da amostra **AdventureWorksLT.** Além disso, certifique-se de que cria uma regra de firewall ao nível do servidor para permitir que o endereço IP do seu cliente aceda à base de dados SQL no servidor. As instruções para adicionar a regra da firewall estão disponíveis no mesmo artigo. Depois de ter criado a sua Base de Dados Azure SQL, certifique-se de que mantém os seguintes valores à mão. Precisa que se liguem à base de dados de um cluster spark.

    * Nome do servidor que acolhe a Base de Dados Azure SQL.
    * Nome de base de dados Azure SQL.
    * Azure SQL Database administra nome/senha de utilizador.

* Estúdio de Gestão de Servidores SQL (SSMS). Siga as instruções do [SSMS para ligar e consultar dados](../../azure-sql/database/connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criar um Bloco de Notas do Jupyter Notebook

Comece por criar um Caderno Jupyter associado ao cluster Spark. Usa este caderno para executar os códigos usados neste artigo.

1. A partir do [portal Azure,](https://portal.azure.com/)abra o seu cluster.
1. Selecione **o caderno Jupyter** por baixo **dos dashboards do Cluster** no lado direito.  Se não vir **os dashboards cluster,** selecione **visão geral** do menu esquerdo. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Caderno de jupyter em Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Caderno de jupyter em Faísca")

   > [!NOTE]  
   > Também pode aceder ao notebook Jupyter no cluster Spark abrindo o seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. No caderno Jupyter, do canto superior direito, clique em **Novo**, e clique em **Spark** para criar um portátil Scala. Os cadernos jupyter no cluster HDInsight Spark também fornecem o kernel **PySpark** para aplicações Python2, e o kernel **PySpark3** para aplicações Python3. Para este artigo, criamos um caderno Scala.

    ![Kernels para o caderno Jupyter em Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels para o caderno Jupyter em Spark")

    Para obter mais informações sobre os kernels, veja [Utilizar kernels de blocos de notas do Jupyter com clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Neste artigo, utilizamos um núcleo Spark (Scala) porque o streaming de dados da Spark para a base de dados SQL é suportado apenas na Base de Dados Scala e Java atualmente. Mesmo que ler e escrever no SQL possa ser feito usando Python, para consistência neste artigo, usamos Scala para todas as três operações.

1. Um novo caderno abre com um nome predefinido, **Sem título.** Clique no nome do caderno e insira um nome à sua escolha.

    ![Fornecer um nome para o bloco de notas](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Fornecer um nome para o bloco de notas")

Agora pode começar a criar a sua aplicação.

## <a name="read-data-from-azure-sql-database"></a>Leia os dados da Base de Dados Azure SQL

Nesta secção, lê-se dados de uma tabela (por exemplo, **SalesLT.Address)** que existe na base de dados AdventureWorks.

1. Num novo caderno Jupyter, numa célula de código, colhe o seguinte corte e substitua os valores do espaço reservado pelos valores para a sua Base de Dados Azure SQL.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. Utilize o corte abaixo para construir um URL JDBC que pode passar para as APIs de moldura de dados spark. O código cria um `Properties` objeto para manter os parâmetros. Colhe o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Utilize o corte abaixo para criar um dataframe com os dados de uma tabela na sua Base de Dados Azure SQL. Neste corte, utilizamos uma `SalesLT.Address` tabela que está disponível como parte da base de dados **AdventureWorksLT.** Colhe o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Agora pode fazer operações no dataframe, tais como obter o esquema de dados:

    ```scala
    sqlTableDF.printSchema
    ```

    Vê-se uma saída semelhante à seguinte imagem:

    ![saída de esquema](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "saída de esquema")

1. Também pode fazer operações como, recuperar as 10 melhores filas.

    ```scala
    sqlTableDF.show(10)
    ```

1. Ou, recuperar colunas específicas do conjunto de dados.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Escreva dados na Base de Dados Azure SQL

Nesta secção, utilizamos um ficheiro CSV de amostra disponível no cluster para criar uma tabela na Base de Dados Azure SQL e povoá-la com dados. O ficheiro CSV da amostra **(HVAC.csv)** está disponível em todos os clusters HDInsight `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` em .

1. Num novo caderno Jupyter, numa célula de código, colhe o seguinte corte e substitua os valores do espaço reservado pelos valores para a sua Base de Dados Azure SQL.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. O seguinte corte constrói um URL JDBC que pode passar para as APIs de moldura de dados spark. O código cria um `Properties` objeto para manter os parâmetros. Colhe o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Utilize o seguinte corte para extrair o esquema dos dados em HVAC.csv e utilize o esquema para carregar os dados do CSV num quadro de dados, `readDf` . Colhe o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Utilize o quadro de `readDf` dados para criar uma tabela temporária, `temphvactable` . Em seguida, use a mesa temporária para criar uma mesa de colmeia, `hvactable_hive` .

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Por fim, utilize a mesa da colmeia para criar uma tabela na Base de Dados Azure SQL. O seguinte corte cria na Base de `hvactable` Dados Azure SQL.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Ligue-se à Base de Dados Azure SQL utilizando SSMS e verifique se vê um `dbo.hvactable` ali.

    a. Inicie o SSMS e ligue-se à Base de Dados Azure SQL, fornecendo detalhes de ligação como indicado na imagem abaixo.

    ![Ligue-se à base de dados SQL utilizando o SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Ligue-se à base de dados SQL utilizando o SSMS1")

    b. A partir do **Object Explorer,** expanda a Base de Dados Azure SQL e o nó table para ver o **dbo.vactable** criado.

    ![Ligue-se à base de dados SQL utilizando o SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Ligue-se à base de dados SQL utilizando o SSMS2")

1. Fazer uma consulta no SSMS para ver as colunas na tabela.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Transmitir dados para a Base de Dados Azure SQL

Nesta secção, transmitimos dados para o que já criou na Base de `hvactable` Dados Azure SQL na secção anterior.

1. Como primeiro passo, certifique-se de que não há registos no `hvactable` . Utilizando SSMS, execute a seguinte consulta na mesa.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Crie um novo caderno Jupyter no cluster HDInsight Spark. Numa célula de código, colhe o seguinte corte e, em seguida, prima **SHIFT + ENTER**:

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. Transmitimos dados do **AVAC.csv** para o `hvactable` . O ficheiro HVAC.csv está disponível no cluster em `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` . No seguinte corte, primeiro obtemos o esquema dos dados para ser transmitido. Depois, criamos um quadro de dados de streaming usando esse esquema. Colhe o corte numa célula de código e prima **SHIFT + ENTER** para executar.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. A saída mostra o esquema de **AVAC.csv**. O `hvactable` tem o mesmo esquema também. A saída lista as colunas na tabela.

    !['hdinsight Apache Spark schema table'](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Esquema da mesa")

1. Por fim, utilize o seguinte corte para ler os dados do AVAC.csv e transmiti-lo para a base de `hvactable` dados Azure SQL. Colhe o corte numa célula de código, substitua os valores do espaço reservado pelos valores da sua Base de Dados Azure SQL e, em seguida, prima **SHIFT + ENTER** para executar.

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

1. Verifique se os dados estão a ser transmitidos para a seguinte consulta no Estúdio de Gestão de `hvactable` Servidores SQL (SSMS). Cada vez que faz escruver a consulta, mostra o número de filas na mesa a aumentar.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Próximos passos

* [Use o cluster HDInsight Spark para analisar dados no armazenamento de data lake](apache-spark-use-with-data-lake-store.md)
* [Carregue dados e execute consultas num cluster Apache Spark em Azure HDInsight](apache-spark-load-data-run-query.md)
* [Use Apache Spark Structured Streaming com Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
