---
title: Acesso Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight
description: Este artigo abrange como trabalhar com Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359599"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Acesso Azure Cosmos DB Cassandra API de Spark on YARN com HDInsight
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo abrange como aceder a Azure Cosmos DB Cassandra API da Spark on YARN com HDInsight-Spark de spark-shell. HDInsight é o Hortonworks Hadoop PaaS da Microsoft no Azure que aproveita o armazenamento de objetos para HDFS, e vem em vários sabores, incluindo [Spark.](../hdinsight/spark/apache-spark-overview.md)  Embora o conteúdo neste documento refira-se a HDInsight-Spark, é aplicável a todas as distribuições de Hadoop.  

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja os fundamentos da ligação à Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Provisão de um cluster HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Reveja as amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md#next-steps)

* [Use cqlsh para validação se preferir](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **A configuração da API de Cassandra em Spark2** - O conector Spark para Cassandra requer que os detalhes da ligação Cassandra sejam inicializados como parte do contexto spark. Quando lança um caderno Jupyter, a sessão de faíscas e o contexto já estão inicializados e não é aconselhável parar e reinitializar o contexto Spark, a menos que esteja completo com todas as configurações definidas como parte da start-up de cadernos Jupyter padrão HDInsight. Uma solução alternativa é adicionar os detalhes da instância Cassandra a Ambari, configuração de serviço Spark2 diretamente. Esta é uma atividade única por cluster que requer um reinício do serviço Spark2.
 
  1. Vá a Ambari, serviço Spark2 e selecione configs

  2. Em seguida, vá a padrão de faíscas personalizadas e adicione uma nova propriedade com o seguinte, e reinicie o serviço Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Acesso Azure Cosmos DB Cassandra API da Spark Shell

A casca de faísca é utilizada para fins de teste/exploração.

* Lançar a concha com as dependências de maven necessárias compatíveis com a versão Spark do seu cluster.

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Acesso Azure Cosmos DB Cassandra API a partir de cadernos Jupyter

HDInsight-Spark vem com serviços de cadernos Zeppelin e Jupyter. Ambos são ambientes de cadernos baseados na web que suportam Scala e Python. Os cadernos são ótimos para análise e colaboração exploratórias interativas, mas não destinados a processos operacionais/produtivos.

Os seguintes cadernos Jupyter podem ser enviados para o seu cluster HDInsight Spark e fornecer amostras prontas para trabalhar com Azure Cosmos DB Cassandra API. Certifique-se de rever o primeiro portátil `1.0-ReadMe.ipynb` para rever a configuração do serviço Spark para ligar à Azure Cosmos DB Cassandra API.

Baixe estes cadernos em [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) para a sua máquina.
  
### <a name="how-to-upload"></a>Como fazer o upload:
Quando lançar jupyter, navegue para Scala. Crie primeiro um diretório e depois carre fique com os cadernos para o diretório. O botão de upload está no lado direito.  

### <a name="how-to-run"></a>Como correr:
Passe pelos cadernos e cada célula de caderno sequencialmente.  Clique no botão de execução na parte superior de cada caderno para executar todas as células, ou shift+insira para cada célula.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Acesso com Azure Cosmos DB Cassandra API do seu programa Spark Scala

Para processos automatizados em produção, os programas Spark são submetidos ao cluster através [de spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Passos seguintes

* [Como construir um programa Spark Scala num IDE e submetê-lo ao cluster HDInsight Spark através da Livy para execução](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Como ligar-se à Azure Cosmos DB Cassandra API de um programa Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Lista completa de amostras de código para trabalhar com a Cassandra API](cassandra-spark-generic.md)
