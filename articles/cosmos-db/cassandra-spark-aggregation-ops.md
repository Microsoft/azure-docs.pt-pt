---
title: Operações agregadas em tabelas da API para Cassandra do Azure Cosmos DB a partir de Spark
description: Este artigo abrange operações básicas de agregação contra as tabelas API API da Azure Cosmos DB Cassandra da Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087626"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Operações agregadas em tabelas da API para Cassandra do Azure Cosmos DB a partir de Spark 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo descreve as operações de agregação básicas em tabelas da API para Cassandra do Azure Cosmos DB a partir do Spark. 

> [!NOTE]
> A filtragem do lado do servidor e a agregação do lado do servidor não é suportada atualmente na Azure Cosmos DB Cassandra API.

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
## <a name="sample-data-generator"></a>Gerador de dados de amostra

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Operação contagem


### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").count
```

**Saída:**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Dataframe API

A contagem contra os dataframes não é suportada atualmente.  A amostra abaixo mostra como executar uma contagem de dados depois de persistir o dataframe para a memória como uma solução alternativa.

Escolha uma [opção]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) de armazenamento a partir das seguintes opções disponíveis, para evitar problemas de "fora de memória":

* MEMORY_ONLY: Esta é a opção de armazenamento padrão. Armazena RDD como objetos java deserializados no JVM. Se o RDD não encaixar na memória, algumas divisórias não serão em cache e são recomuedidas em voo cada vez que são necessárias.

* MEMORY_AND_DISK: Armazena RDD como objetos Java deserializados no JVM. Se o RDD não encaixar na memória, guarde as divisórias que não cabem no disco e, sempre que necessário, leia-as a partir do local onde estão armazenadas.

* MEMORY_ONLY_SER (Java/Scala): Armazena RDD como objetos java serializados- matriz de um byte por partição. Esta opção é eficiente em termos de espaço quando comparada com objetos deserializados, especialmente quando se utiliza um serializador rápido, mas mais intensivo de CPU para ler.

* MEMORY_AND_DISK_SER (Java/Scala): Esta opção de armazenamento é como MEMORY_ONLY_SER, a única diferença é que derrama divisórias que não se encaixam na memória do disco em vez de as recomprê-las quando são necessárias.

* DISK_ONLY: Armazena apenas as divisórias RDD no disco.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: O mesmo que os níveis acima, mas replica cada partição em dois nós de cluster.

* OFF_HEAP (experimental): Semelhante a MEMORY_ONLY_SER, mas armazena os dados em memória off-heap, e requer que a memória off-heap seja ativada antes do tempo. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Operação média

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Saída:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Saída:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Saída:**
```
16.016000175476073
```

## <a name="min-operation"></a>Operação Min

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Saída:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Saída:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Saída:**
```
11.33
```

## <a name="max-operation"></a>Operação Max

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Dataframe API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Saída:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Saída:**
```
22.45
```

## <a name="sum-operation"></a>Operação sum

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Saída:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Dataframe API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Saída:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Saída:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Operação superior ou comparável

### <a name="rdd-api"></a>RDD API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Saída:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Dataframe API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Saída:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Passos seguintes

Para realizar operações de cópia de mesa, consulte:

* [Operações de cópia de mesa](cassandra-spark-table-copy-ops.md)
