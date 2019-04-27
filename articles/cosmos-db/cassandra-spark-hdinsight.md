---
title: Acesso do Azure Cosmos DB Cassandra API do Spark no YARN com o HDInsight
description: Este artigo aborda como trabalhar com a API de Cassandra do Azure Cosmos DB do Spark no YARN com o HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893707"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Acesso do Azure Cosmos DB Cassandra API do Spark no YARN com o HDInsight

Este artigo aborda como aceder a Cassandra API do Azure Cosmos DB a partir do Spark no YARN com o Spark do HDInsight a partir da shell do spark. HDInsight é Hortonworks Hadoop PaaS da Microsoft do Azure, que tira partido do armazenamento de objetos para o HDFS e possui vários tipos, incluindo [Spark](../hdinsight/spark/apache-spark-overview.md).  Enquanto o conteúdo neste documento referencia Spark do HDInsight, é aplicável a todas as distribuições Hadoop.  

## <a name="prerequisites"></a>Pré-requisitos

* [Aprovisionar a API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja as noções básicas de conexão a Cassandra API do Azure Cosmos DB](cassandra-spark-generic.md)

* [Aprovisionar um cluster do HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Reveja os exemplos de código para trabalhar com a API de Cassandra](cassandra-spark-generic.md#next-steps)

* [Utilize cqlsh para validação, se preferir por isso](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configuração da API de Cassandra no Spark2** -conector do Spark para o Cassandra requer que a ligação de Cassandra detalhes para ser inicializado como parte do contexto do Spark. Quando inicia um bloco de notas do Jupyter, a sessão do spark e o contexto já foram inicializados e não é recomendado parar e reinicializar o contexto do Spark, a menos que seja concluída com cada configuração definida como parte da inicialização de bloco de notas Jupyter do HDInsight padrão. Uma solução alternativa é adicionar os detalhes de instância do Cassandra para Ambari, a configuração do serviço de Spark2 diretamente. Esta é uma atividade de uso individual por cluster, que é necessário reiniciar o serviço Spark2.
 
  1. Ir para o Ambari, Spark2 serviço e selecionadas configurações

  2. Em seguida, vá para as predefinições de spark2 personalizadas e adicionar uma nova propriedade com o seguinte e reinicie o serviço de Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>API de Cassandra do acesso do Azure Cosmos DB a partir da shell do Spark

Shell do Spark é utilizada para fins de teste/exploração.

* Inicie a shell do spark com as dependências necessárias do maven compatíveis com a versão do seu cluster do Spark.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Execute algumas operações DDL e DML

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>API de Cassandra do acesso do Azure Cosmos DB a partir de blocos de notas do Jupyter

Spark do HDInsight é fornecido com os serviços de bloco de notas do Zeppelin e Jupyter. Eles são os dois ambientes de bloco de notas e baseado na web que suportam o Scala e Python. Blocos de notas são ótimos para análises exploratórias interativas e colaboração, mas não se destina processos operacionais/productionized.

Os seguintes blocos de notas do Jupyter podem ser carregados no seu cluster do HDInsight Spark e fornecem exemplos de prontos para trabalhar com a API de Cassandra do Azure Cosmos DB. Certifique-se de que reveja o primeiro bloco de notas `1.0-ReadMe.ipynb` para rever a configuração de serviço do Spark para ligar à API de Cassandra do Azure Cosmos DB.

Baixe estes blocos de notas sob [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) para o seu computador.
  
### <a name="how-to-upload"></a>Como carregar:
Quando inicia o Jupyter, navegue para Scala. Crie primeiro um diretório e, em seguida, carregue os blocos de notas para o diretório. O botão de carregamento está na parte superior, do lado do lado direito.  

### <a name="how-to-run"></a>Como executar:
Execute os blocos de notas e cada célula de bloco de notas em seqüência.  Clique no botão para executar na parte superior de cada bloco de notas para executar todas as células, ou shift + enter para cada célula.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acesso com a API de Cassandra do Azure Cosmos DB a partir de seu programa Spark Scala

Para processos automatizados na produção, os programas do Spark são submetidos ao cluster através de [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Passos Seguintes

* [Como criar um Scala Spark programar num IDE e enviá-lo para o cluster do Spark do HDInsight através do Livy para execução](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Como ligar à API de Cassandra do Azure Cosmos DB a partir de um programa do Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa das amostras de código para trabalhar com a API de Cassandra](cassandra-spark-generic.md)
