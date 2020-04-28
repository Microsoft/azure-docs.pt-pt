---
title: Access Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight
description: Este artigo abrange como trabalhar com a Azure Cosmos DB Cassandra API da Spark on YARN com hDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887637"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Access Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight

Este artigo abrange como aceder a Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight-Spark da spark-shell. HDInsight é o Hortonworks Hadoop PaaS da Microsoft no Azure que aproveita o armazenamento de objetos para hDFS, e vem em vários sabores, incluindo [Spark](../hdinsight/spark/apache-spark-overview.md).  Embora o conteúdo deste documento se refira ao HDInsight-Spark, é aplicável a todas as distribuições hadoop.  

## <a name="prerequisites"></a>Pré-requisitos

* [Provision Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja os fundamentos da ligação à Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Provision um cluster HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Reveja as amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

* [Use cqlsh para validação se preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **A configuração da Cassandra API em Spark2** - O conector Spark para Cassandra exige que os detalhes da ligação Cassandra sejam inicializados como parte do contexto Spark. Quando lança um portátil Jupyter, a sessão de faísca e o contexto já estão inicializados e não é aconselhável parar e reinicializar o contexto Spark a menos que esteja completo com todas as configurações definidas como parte da start-up do portátil padrão Do Jupyter hDInsight. Uma sobra é adicionar os detalhes da instância de Cassandra a Ambari, configuração do serviço Spark2 diretamente. Esta é uma atividade única por cluster que requer um reinício do serviço Spark2.
 
  1. Vá ao serviço Ambari, Spark2 e selecione configs

  2. Em seguida, vá a erros personalizados de ignição 2 e adicione uma nova propriedade com o seguinte, e reinicie o serviço Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Access Azure Cosmos DB Cassandra API da Spark Shell

A casca de faísca é utilizada para fins de teste/exploração.

* Lance uma casca de faísca com as dependências maven necessárias compatíveis com a versão Spark do seu cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Executar algumas operações dDL e DML

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

* Executar operações crud

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Access Azure Cosmos DB Cassandra API dos cadernos Jupyter

HDInsight-Spark vem com serviços de caderno zeppelin e Jupyter. Ambos são ambientes de cadernos baseados na web que suportam Scala e Python. Os cadernos são ótimos para análise seletiva interativa e colaboração, mas não destinam-se a processos operacionais/produtivos.

Os seguintes cadernos Jupyter podem ser enviados para o seu cluster HDInsight Spark e fornecer amostras prontas para trabalhar com a Azure Cosmos DB Cassandra API. Certifique-se de rever `1.0-ReadMe.ipynb` o primeiro caderno para rever a configuração do serviço Spark para ligar à API Da API do Azure Cosmos DB Cassandra.

Descarregue estes cadernos em [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) para a sua máquina.
  
### <a name="how-to-upload"></a>Como fazer o upload:
Quando lançar jupyter, navegue até Scala. Crie primeiro um diretório e depois carregue os cadernos para o diretório. O botão de carregamento está no lado superior, direito.  

### <a name="how-to-run"></a>Como correr:
Passe pelos cadernos, e cada célula de caderno sequencialmente.  Clique no botão de execução na parte superior de cada portátil para executar todas as células ou alternar+para cada célula.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acesso com API Db Cassandra Da Azure Cosmos do seu programa Spark Scala

Para processos automatizados em produção, os programas Spark são submetidos ao cluster através [de spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Passos seguintes

* [Como construir um programa Spark Scala num IDE e submetê-lo ao cluster HDInsight Spark através da Livy para execução](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Como ligar-se à Azure Cosmos DB Cassandra API de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa de amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md)
