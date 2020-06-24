---
title: Criar ou inserir dados na Azure Cosmos DB Cassandra API da Spark
description: Este artigo detalha como inserir dados de amostra nas tabelas API AZURE Cosmos DB Cassandra
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 56899b7d81f17e678026b1aad7f3e2d9f8b9b0b0
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260529"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Criar/inserir dados na Azure Cosmos DB Cassandra API da Spark
 
Este artigo descreve como inserir dados de amostra numa tabela em Azure Cosmos DB Cassandra API da Spark.

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
## <a name="dataframe-api"></a>Dataframe API

### <a name="create-a-dataframe-with-sample-data"></a>Criar um Dataframe com dados de amostra

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
> A funcionalidade "Criar se não existir", a nível de linha, ainda não está suportada.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Persistir para Azure Cosmos DB Cassandra API

Ao guardar dados, também pode definir as definições de política de tempo para viver e consistência, como mostrado no exemplo seguinte:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> O TTL de nível de coluna ainda não está suportado.

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>API de Base de Dados Distribuída Resiliente (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Criar um RDD com dados de amostra
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
> Criar se não existir funcionalidade ainda não está suportado.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Persistir para Azure Cosmos DB Cassandra API

Ao guardar dados para a API de Cassandra, também pode definir as definições de política de tempo-a-viver e de consistência, como mostrado no exemplo seguinte:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Passos seguintes

Depois de inserir dados na tabela API API da Azure Cosmos DB Cassandra, dirija-se aos seguintes artigos para efetuar outras operações sobre os dados armazenados na API de Cosmos DB Cassandra:
 
* [Ler operações](cassandra-spark-read-ops.md)
* [Operações de upsert](cassandra-spark-upsert-ops.md)
* [Eliminar operações](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia de mesa](cassandra-spark-table-copy-ops.md)

