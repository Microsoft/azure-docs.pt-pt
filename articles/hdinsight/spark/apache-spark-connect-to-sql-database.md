---
title: Usar Apache Spark para ler e gravar dados no banco de dado SQL do Azure
description: Saiba como configurar uma conexão entre o cluster HDInsight Spark e um banco de dados SQL do Azure para ler, gravar e transmitir dados em um banco de dados SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: cc225f4cae3935212844c19464afc716092e73ca
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035198"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Usar o cluster HDInsight Spark para ler e gravar dados no banco de dado SQL do Azure

Saiba como conectar um cluster de Apache Spark no Azure HDInsight com um banco de dados SQL do Azure e, em seguida, ler, gravar e transmiti-los para o SQL Database. As instruções neste artigo usam um [Jupyter Notebook](https://jupyter.org/) para executar os trechos de código escalares. No entanto, você pode criar um aplicativo autônomo em escala ou Python e executar as mesmas tarefas.

## <a name="prerequisites"></a>Pré-requisitos

* Cluster Azure HDInsight Spark *.  Siga as instruções em [criar um cluster apache Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

* Banco de dados SQL do Azure. Siga as instruções em [criar um banco de dados SQL do Azure](../../sql-database/sql-database-get-started-portal.md). Certifique-se de criar um banco de dados com o esquema de exemplo **AdventureWorksLT** Além disso, certifique-se de criar uma regra de firewall no nível de servidor para permitir que o endereço IP do cliente acesse o banco de dados SQL no servidor. As instruções para adicionar a regra de firewall estão disponíveis no mesmo artigo. Depois de criar o banco de dados SQL do Azure, certifique-se de manter os valores a seguir à mão. Você precisa deles para se conectar ao banco de dados de um cluster Spark.

    * Nome do servidor que hospeda o banco de dados SQL do Azure.
    * Nome do banco de dados SQL do Azure.
    * Nome de usuário/senha do administrador do banco de dados SQL do Azure.

* SQL Server Management Studio (SSMS). Siga as instruções em [usar o SSMS para conectar e consultar dados](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criar um Jupyter Notebook

Comece criando um [Jupyter Notebook](https://jupyter.org/) associado ao cluster Spark. Use este bloco de anotações para executar os trechos de código usados neste artigo.

1. No [portal do Azure](https://portal.azure.com/), abra o cluster.
1. Selecione o **Notebook Jupyter** sob **painéis de cluster** no lado direito.  Se você não vir os **painéis de cluster**, selecione **visão geral** no menu à esquerda. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Jupyter Notebook no notebook Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter no Spark")

   > [!NOTE]  
   > Você também pode acessar o notebook Jupyter no cluster do Spark abrindo a seguinte URL no seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. No notebook Jupyter, no canto superior direito, clique em **novo**e em **Spark** para criar um notebook escala. Os notebooks Jupyter no cluster HDInsight Spark também fornecem o kernel **PySpark** para aplicativos Python2 e o kernel **PySpark3** para aplicativos Python3. Para este artigo, criamos um notebook escala.

    ![Kernels for Jupyter notebook em](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "kernels do Spark para o notebook Jupyter no Spark")

    Para obter mais informações sobre os kernels, veja [Utilizar kernels de blocos de notas do Jupyter com clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Neste artigo, usamos um kernel Spark (escala) porque há suporte para streaming de dados do Spark para o banco de dado SQL apenas em escala e Java no momento. Embora a leitura e a gravação no SQL possam ser feitas usando o Python, para consistência neste artigo, usamos escalabilidade para todas as três operações.

1. Isso abre um novo bloco de anotações com um nome padrão, sem **título**. Clique no nome do bloco de anotações e insira um nome de sua escolha.

    ![Forneça um nome para o bloco de notas](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Forneça um nome para o bloco de notas")

Agora você pode começar a criar seu aplicativo.

## <a name="read-data-from-azure-sql-database"></a>Ler dados do banco de dados SQL do Azure

Nesta seção, você lê os dados de uma tabela (por exemplo, **tabela SalesLT. Address**) que existe no banco de dados AdventureWorks.

1. Em um novo notebook Jupyter, em uma célula de código, Cole o trecho a seguir e substitua os valores de espaço reservado pelos valores para seu banco de dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. Use o trecho abaixo para criar uma URL JDBC que você pode passar para as APIs do dataframe do Spark. O código cria um objeto `Properties` para manter os parâmetros. Cole o trecho em uma célula de código e pressione **Shift + Enter** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Use o trecho abaixo para criar um dataframe com os dados de uma tabela no banco de dados SQL do Azure. Neste trecho, usamos uma tabela **tabela SalesLT. Address** que está disponível como parte do banco de dados **AdventureWorksLT** . Cole o trecho em uma célula de código e pressione **Shift + Enter** para executar.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Agora você pode executar operações no dataframe, como obter o esquema de dados:

       sqlTableDF.printSchema

    Você verá uma saída semelhante à seguinte:

    ![](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "saída do esquema") de saída do esquema

1. Você também pode executar operações como, recuperar as 10 primeiras linhas.

       sqlTableDF.show(10)

1. Ou recupere colunas específicas do conjunto de informações.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Gravar dados no banco de dado SQL do Azure

Nesta seção, usamos um arquivo CSV de exemplo disponível no cluster para criar uma tabela no banco de dados SQL do Azure e preenchê-la com os Data. O arquivo CSV de exemplo (**HVAC. csv**) está disponível em todos os clusters HDInsight em `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Em um novo notebook Jupyter, em uma célula de código, Cole o trecho a seguir e substitua os valores de espaço reservado pelos valores para seu banco de dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. O trecho a seguir cria uma URL JDBC que você pode passar para as APIs do dataframe do Spark. O código cria um objeto `Properties` para manter os parâmetros. Cole o trecho em uma célula de código e pressione **Shift + Enter** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Use o trecho a seguir para extrair o esquema dos dados no HVAC. csv e usar o esquema para carregar os dados do CSV em um dataframe, `readDf`. Cole o trecho em uma célula de código e pressione **Shift + Enter** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Use o `readDf` dataframe para criar uma tabela temporária, `temphvactable`. Em seguida, use a tabela temporária para criar uma tabela do hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Por fim, use a tabela Hive para criar uma tabela no banco de dados SQL do Azure. O trecho a seguir cria `hvactable` no banco de dados SQL do Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Conecte-se ao banco de dados SQL do Azure usando o SSMS e verifique se você vê um `dbo.hvactable` lá.

    a. Inicie o SSMS e conecte-se ao banco de dados SQL do Azure fornecendo detalhes de conexão, conforme mostrado na captura de tela abaixo.

    ![Conectar-se ao banco de dados SQL usando o SSMS1](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "conectar ao banco de dados SQL usando o SSMS1")

    b. No **pesquisador de objetos**, expanda o banco de dados SQL do Azure e o nó da tabela para ver o **dbo. hvactable** criado.

    ![Conectar-se ao banco de dados SQL usando o SSMS2](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "conectar ao banco de dados SQL usando o SSMS2")

1. Execute uma consulta no SSMS para ver as colunas na tabela.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Transmita dados para o Azure SQL Database

Nesta seção, transmitimos dados para o **hvactable** que você já criou no Azure SQL Database na seção anterior.

1. Como uma primeira etapa, verifique se não há registros no **hvactable**. Usando o SSMS, execute a consulta a seguir na tabela.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Crie um novo notebook Jupyter no cluster HDInsight Spark. Em uma célula de código, Cole o trecho a seguir e pressione **Shift + Enter**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Transmitimos dados do **HVAC. csv** para o hvactable. O arquivo HVAC. csv está disponível no cluster em `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`. No trecho a seguir, primeiro obtemos o esquema dos dados a serem transmitidos. Em seguida, criamos um dataframe de streaming usando esse esquema. Cole o trecho em uma célula de código e pressione **Shift + Enter** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. A saída mostra o esquema de **HVAC. csv**. O **hvactable** também tem o mesmo esquema. A saída lista as colunas na tabela.

    esquema de ![tabela de esquema Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "do hdinsight da tabela")

1. Por fim, use o trecho a seguir para ler dados do HVAC. csv e transmiti-los para o **hvactable** no banco de dados SQL do Azure. Cole o trecho em uma célula de código, substitua os valores de espaço reservado pelos valores de seu banco de dados SQL do Azure e pressione **Shift + Enter** para executar.

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

1. Verifique se os dados estão sendo transmitidos para o **hvactable** executando a seguinte consulta no SQL Server Management Studio (SSMS). Toda vez que você executa a consulta, ela mostra o número de linhas na tabela que aumentam.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Passos seguintes

* [Usar o cluster HDInsight Spark para analisar dados no Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Processar eventos de streaming estruturados usando o EventHub](apache-spark-eventhub-structured-streaming.md)
* [Usar Apache Spark streaming estruturado com Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
