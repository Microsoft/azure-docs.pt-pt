---
title: Utilizar o Apache Spark para ler e escrever dados para a base de dados SQL do Azure
description: Saiba como configurar uma ligação entre o cluster do HDInsight Spark e uma base de dados SQL do Azure para ler dados, escrever dados e transmitir dados num banco de dados SQL
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: 84946083146517146ad9aeb48693230aaaaf7943
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701184"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Utilizar o cluster do HDInsight Spark para ler e escrever dados para a base de dados SQL do Azure

Saiba como ligar um cluster do Apache Spark no HDInsight do Azure com uma base de dados SQL do Azure e, em seguida, ler, escrever e transmitir dados para a base de dados SQL. As instruções neste artigo utilizam uma [bloco de notas do Jupyter](https://jupyter.org/) para executar os fragmentos de código Scala. No entanto, pode criar uma aplicação autónoma em Scala ou Python e executar as mesmas tarefas. 

## <a name="prerequisites"></a>Pré-requisitos

* **Cluster do Spark do HDInsight do Azure**.  Siga as instruções em [criar um cluster do Apache Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

* **Base de dados SQL do Azure**. Siga as instruções em [criar uma base de dados SQL do Azure](../../sql-database/sql-database-get-started-portal.md). Certifique-se de criar uma base de dados com o exemplo **AdventureWorksLT** esquema e os dados. Além disso, certifique-se de que criar uma regra de firewall ao nível do servidor para permitir o endereço IP do seu cliente para a base de dados SQL no servidor de acesso. As instruções para adicionar a regra de firewall está disponível no mesmo artigo. Depois de criar a base de dados SQL do Azure, certifique-se de que manter os seguintes valores úteis. Precisará destas para ligar à base de dados a partir de um cluster do Spark.

    * Nome do servidor que aloja a base de dados SQL do Azure.
    * Nome da base de dados SQL do Azure.
    * Azure nome de utilizador do administrador de banco de dados do SQL / palavra-passe.

* **SQL Server Management Studio**. Siga as instruções em [utilizar SSMS para ligar e consultar dados](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter 

Comece por criar um [bloco de notas do Jupyter](https://jupyter.org/) associada ao cluster do Spark. Utilize este bloco de notas para executar os fragmentos de código usados neste artigo. 

1. Partir do [portal do Azure](https://portal.azure.com/), abra o seu cluster.
1. Selecione **bloco de notas do Jupyter** por baixo **dashboards de clusters** no lado direito.  Se não vir **dashboards de clusters**, clique em **descrição geral** no menu à esquerda, no painel. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    ![Bloco de notas do Jupyter no Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "bloco de notas do Jupyter no Spark")
   
   > [!NOTE]  
   > Também pode acessar o bloco de notas do Jupyter no cluster do Spark ao abrir o URL seguinte no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. No bloco de notas Jupyter, no canto superior direito, clique em **New**e, em seguida, clique em **Spark** para criar um bloco de notas Scala. Blocos de notas do Jupyter no cluster do HDInsight Spark fornecem também a **PySpark** kernel para aplicativos de Python2 e o **PySpark3** kernel para aplicativos de Python3. Neste artigo, vamos criar um bloco de notas Scala.
   
    ![Kernels para o bloco de notas do Jupyter no Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels para o bloco de notas do Jupyter no Spark")

    Para obter mais informações sobre os kernels, veja [Utilizar kernels de blocos de notas do Jupyter com clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Neste artigo, utilizamos um kernel do Spark (Scala) porque os dados de transmissão em fluxo do Spark na base de dados SQL só são suportados em Scala e Java atualmente. Apesar de leitura e gravação em SQL podem ser feitos com o Python, para manter a consistência neste artigo, vamos utilizar o Scala para todas as operações de três.

1. Esta ação abre um novo notebook com um nome predefinido, **sem título**. Clique no nome do bloco de notas e introduza um nome à sua escolha.

    ![Forneça um nome para o bloco de notas](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Forneça um nome para o bloco de notas")

Agora pode começar a criar a sua aplicação.
    
## <a name="read-data-from-azure-sql-database"></a>Ler dados a partir da base de dados SQL do Azure

Nesta secção, é possível ler dados de uma tabela (por exemplo, **SalesLT.Address**) que existe na base de dados AdventureWorks.

1. Num novo bloco de notas Jupyter, numa célula de código, cole o fragmento seguinte e substitua os valores de marcador de posição pelos valores da base de dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. Utilize o fragmento abaixo para compilar um URL de JDBC, que pode passar para o pacote de dados de Spark APIs cria um `Properties` objeto para manter os parâmetros. Cole o fragmento numa célula de código e prima **SHIFT + ENTER** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Utilize o fragmento abaixo para criar um dataframe com os dados de uma tabela na base de dados SQL do Azure. Neste fragmento, usamos uma **SalesLT.Address** tabela que está disponível como parte dos **AdventureWorksLT** base de dados. Cole o fragmento numa célula de código e prima **SHIFT + ENTER** para executar.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Agora pode executar operações no pacote de dados, como obter o esquema de dados:

       sqlTableDF.printSchema
   
    Verá um resultado semelhante ao seguinte:

    ![Forneça um nome para o bloco de notas](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Forneça um nome para o bloco de notas")

1. Também pode efetuar operações como obter as primeiras 10 linhas.

       sqlTableDF.show(10)

1. Em alternativa, obter colunas específicas do conjunto de dados.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Escrever dados na base de dados SQL do Azure

Nesta secção, usamos um ficheiro CSV de exemplo disponível no cluster para criar uma tabela na base de dados SQL do Azure e preenchê-lo com dados. Ficheiro CSV de exemplo (**Hvac**) está disponível em todos os clusters do HDInsight em `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Num novo bloco de notas Jupyter, numa célula de código, cole o fragmento seguinte e substitua os valores de marcador de posição pelos valores da base de dados SQL do Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Prima **SHIFT + ENTER** para executar a célula do código.  

1. O fragmento seguinte cria um URL de JDBC, que pode passar para o pacote de dados de Spark APIs cria um `Properties` objeto para manter os parâmetros. Cole o fragmento numa célula de código e prima **SHIFT + ENTER** para executar.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Utilize o seguinte fragmento para extrair o esquema dos dados na Hvac e utilize o esquema para carregar os dados do CSV no pacote de dados, `readDf`. Cole o fragmento numa célula de código e prima **SHIFT + ENTER** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Utilize o `readDf` dataframe para criar uma tabela temporária, `temphvactable`. Em seguida, utilize a tabela temporária para criar uma tabela do hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Por fim, utilize a tabela de hive para criar uma tabela na base de dados SQL do Azure. O fragmento seguinte cria `hvactable` na base de dados SQL do Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Ligar à base de dados SQL do Azure com o SSMS e certifique-se de que vê um `dbo.hvactable` lá.

    a. Iniciar o SSMS e ligue à base de dados SQL do Azure, fornecendo os detalhes da ligação, conforme mostrado na captura de ecrã abaixo.

    ![Ligar à base de dados SQL com o SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "ligar à base de dados SQL com o SSMS")

    b. No Object Explorer, expanda a base de dados SQL do Azure e o nó de tabela para ver os **dbo.hvactable** criado.

    ![Ligar à base de dados SQL com o SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "ligar à base de dados SQL com o SSMS")

1. Execute uma consulta no SSMS para ver as colunas na tabela.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Dados de Stream na base de dados SQL do Azure

Nesta secção, vamos transmitir dados para o **hvactable** que já criou na base de dados SQL do Azure na secção anterior.

1. Como primeiro passo, certifique-se de que não há registros na **hvactable**. Com o SSMS, execute a seguinte consulta na tabela.

       DELETE FROM [dbo].[hvactable]

1. Crie um novo bloco de notas do Jupyter no cluster do HDInsight Spark. Numa célula de código, cole o fragmento seguinte e, em seguida, prima **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Podemos transmitir dados a partir da **Hvac** no hvactable. Ficheiro de Hvac está disponível no cluster em */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. No trecho a seguir, vamos primeiro o esquema dos dados a ser transmitidos em fluxo. Em seguida, vamos criar um pacote de dados de transmissão em fluxo usando esse esquema. Cole o fragmento numa célula de código e prima **SHIFT + ENTER** para executar.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. A saída mostra o esquema do **Hvac**. O **hvactable** tem o mesmo esquema. A saída lista as colunas na tabela.

    ![Esquema de tabela](./media/apache-spark-connect-to-sql-database/schema-of-table.png "esquema de tabela")

1. Por fim, utilize o seguinte fragmento para ler dados a partir do Hvac e transmiti-lo para o **hvactable** na base de dados SQL do Azure. Cole o fragmento numa célula de código, substitua os valores de marcador de posição pelos valores da base de dados SQL do Azure e, em seguida, prima **SHIFT + ENTER** para executar.

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

1. Certifique-se de que os dados são a ser transmitidos para o **hvactable** executando a seguinte consulta no SQL Server Management Studio (SSMS). Sempre que executar a consulta, o número de linhas mostra o aumento de tabela.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar o cluster do Spark do HDInsight para analisar dados no armazenamento do Data Lake](apache-spark-use-with-data-lake-store.md)
* [Processar eventos de transmissão em fluxo estruturados com o EventHub](apache-spark-eventhub-structured-streaming.md)
* [Utilizar o Apache Spark estruturado de transmissão em fluxo com o Apache Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
