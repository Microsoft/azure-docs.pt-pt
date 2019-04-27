---
title: Dados de tabela de leitura Cassandra API com o Spark
titleSufix: Azure Cosmos DB
description: Este artigo descreve como ler dados a partir de tabelas de Cassandra API no Azure Cosmos DB.
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893406"
---
<!--Verify sucessfully-->
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Ler dados a partir de tabelas Cassandra API do Azure Cosmos DB com o Spark

 Este artigo descreve como ler os dados armazenados na API de Cassandra do Azure Cosmos DB do Spark.

## <a name="cassandra-api-configuration"></a>Configuração da API de Cassandra
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.cn")
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

### <a name="read-table-using-sessionreadformat-command"></a>Tabela de leitura com o comando de session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Tabela de leitura usando spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Colunas específicas na tabela de leitura

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Aplicar filtros

Não é atualmente suportada propagação predicada, os exemplos abaixo refletem a filtragem do lado do cliente. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>API DE RDD

### <a name="read-table"></a>Tabela de leitura
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Colunas específicas na tabela de leitura

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Vistas SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Criar uma vista de temporária de um pacote de dados

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Executar consultas em relação a vista

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Passos Seguintes

Seguem-se artigos adicionais sobre como trabalhar com a API de Cassandra do Azure Cosmos DB do Spark:

 * [Operações de Upsert](cassandra-spark-upsert-ops.md)
 * [Operações de eliminação](cassandra-spark-delete-ops.md)
 * [Operações de agregação](cassandra-spark-aggregation-ops.md)
 * [Operações de cópia da tabela](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->