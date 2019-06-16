---
title: Criar/inserir dados na API de Cassandra do Azure Cosmos DB do Spark
description: Este artigo mostra em detalhe como inserir dados de exemplo em tabelas Cassandra API do Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aea646e7a390d5b53f0d4b388cfecd0c80fb19da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60894050"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Criar/inserir dados na API de Cassandra do Azure Cosmos DB do Spark
 
Este artigo descreve como inserir dados de exemplo numa tabela na API de Cassandra do Azure Cosmos DB do Spark.

## <a name="cassandra-api-configuration"></a>Configuração da API de Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Pacote de API de dados

### <a name="create-a-dataframe-with-sample-data"></a>Crie um Dataframe com dados de exemplo

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> A funcionalidade "Criar se não existir", num nível de linha, ainda não é suportada.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Manter-se ao Azure Cosmos DB Cassandra API

Ao guardar os dados, também pode definir time-to-live e definições de política de consistência, conforme mostrado no exemplo a seguir:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> TTL de nível de coluna ainda não é suportado.

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>API de resiliente base de dados distribuída (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Criar um RDD com dados de exemplo
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Criar, se ainda não existe funcionalidade ainda não é suportada.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Manter-se ao Azure Cosmos DB Cassandra API

Ao guardar os dados para a API de Cassandra, também pode definir time-to-live e definições de política de consistência, conforme mostrado no exemplo a seguir:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Passos Seguintes

Depois de inserir dados na tabela Cassandra API do Azure Cosmos DB, avance para os seguintes artigos para executar outras operações nos dados armazenados no Cassandra API do Cosmos DB:
 
* [operações de leitura](cassandra-spark-read-ops.md)
* [Operações de Upsert](cassandra-spark-upsert-ops.md)
* [Operações de eliminação](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia da tabela](cassandra-spark-table-copy-ops.md)

