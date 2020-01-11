---
title: API do Cassandra de Azure Cosmos DB de acesso do Spark no YARN com o HDInsight
description: Este artigo aborda como trabalhar com Azure Cosmos DB API do Cassandra do Spark no YARN com o HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887637"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>API do Cassandra de Azure Cosmos DB de acesso do Spark no YARN com o HDInsight

Este artigo aborda como acessar Azure Cosmos DB API do Cassandra do Spark no YARN com o HDInsight-Spark do Spark-Shell. O HDInsight é a PaaS do Hortonworks do Hadoop da Microsoft no Azure que aproveita o armazenamento de objetos para o HDFS e vem em vários tipos, incluindo o [Spark](../hdinsight/spark/apache-spark-overview.md).  Embora o conteúdo deste documento referencie o HDInsight-Spark, ele é aplicável a todas as distribuições do Hadoop.  

## <a name="prerequisites"></a>Pré-requisitos

* [Provisionar Azure Cosmos DB API do Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Examine as noções básicas de como se conectar ao Azure Cosmos DB API do Cassandra](cassandra-spark-generic.md)

* [Provisionar um cluster HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Examine os exemplos de código para trabalhar com API do Cassandra](cassandra-spark-generic.md#next-steps)

* [Use cqlsh para validação, se preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **API do Cassandra configuração no Spark2** -o conector do Spark para Cassandra requer que os detalhes da conexão Cassandra sejam inicializados como parte do contexto do Spark. Quando você inicia um notebook Jupyter, a sessão e o contexto do Spark já estão inicializados e não é aconselhável parar e reinicializar o contexto do Spark, a menos que ele seja concluído com cada conjunto de configurações como parte da inicialização do notebook Jupyter padrão do HDInsight. Uma solução alternativa é adicionar os detalhes da instância do Cassandra ao Ambari, a configuração do serviço Spark2 diretamente. Essa é uma atividade de uso único por cluster que requer uma reinicialização do serviço Spark2.
 
  1. Vá para Ambari, serviço Spark2 e selecione configurações

  2. Em seguida, vá para spark2 personalizado-padrões e adicione uma nova propriedade com o seguinte e reinicie o serviço Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>API do Cassandra de Azure Cosmos DB de acesso do shell do Spark

O Shell do Spark é usado para fins de teste/exploração.

* Inicie o Spark-shell com as dependências do Maven necessárias compatíveis com a versão do Spark do seu cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Executar algumas operações DDL e DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Executar operações CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>API do Cassandra de Azure Cosmos DB de acesso de blocos de anotações do Jupyter

O HDInsight-Spark vem com os serviços de notebook Zeppelin e Jupyter. Eles são ambientes de blocos de anotações baseados na Web que dão suporte a escala e Python. Os notebooks são ótimos para colaboração e análise exploratório interativa, mas não destinados a processos operacionais/em produção.

Os seguintes blocos de anotações do Jupyter podem ser carregados em seu cluster HDInsight Spark e fornecer exemplos prontos para trabalhar com Azure Cosmos DB API do Cassandra. Certifique-se de examinar o primeiro `1.0-ReadMe.ipynb` de bloco de anotações para examinar a configuração do serviço Spark para se conectar ao Azure Cosmos DB API do Cassandra.

Baixe esses blocos de anotações em [Azure-Cosmos-DB-Cassandra-API-Spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) em seu computador.
  
### <a name="how-to-upload"></a>Como carregar:
Ao iniciar o Jupyter, navegue até escala. Crie um diretório primeiro e, em seguida, carregue os blocos de anotações no diretório. O botão carregar está na parte superior, lado direito.  

### <a name="how-to-run"></a>Como executar:
Execute os notebooks e cada célula do bloco de anotações em sequência.  Clique no botão Executar na parte superior de cada bloco de anotações para executar todas as células ou Shift + Enter para cada célula.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acesso com Azure Cosmos DB API do Cassandra de seu programa do Spark escalar

Para processos automatizados na produção, os programas do Spark são enviados ao cluster por meio do [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Passos seguintes

* [Como criar um programa do Spark escalar em um IDE e enviá-lo para o cluster HDInsight Spark por meio do Livy para execução](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Como se conectar ao Azure Cosmos DB API do Cassandra de um programa do Spark escalar](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa de exemplos de código para trabalhar com API do Cassandra](cassandra-spark-generic.md)
