---
title: Leia os dados da tabela da API de Cassandra usando a Spark
titleSufix: Azure Cosmos DB
description: Este artigo descreve como ler dados das tabelas da API de Cassandra em Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073501"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Leia os dados das tabelas Azure Cosmos DB Cassandra API usando Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Este artigo descreve como ler dados armazenados em Azure Cosmos DB Cassandra API da Spark.

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

### <a name="read-table-using-sessionreadformat-command"></a>Leia a tabela usando o comando sessão.read.formato

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Leia a tabela usando spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Leia colunas específicas na tabela

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

Você pode empurrar predicados para baixo para a base de dados para permitir consultas de faíscas melhor otimizadas. Um predicado é uma condição para uma consulta que devolve verdadeiro ou falso, tipicamente localizado na cláusula WHERE. Um pré-empurrão filtra os dados na consulta da base de dados, reduzindo o número de entradas obtidas na base de dados e melhorando o desempenho da consulta. Por predefinição, a API do Conjunto de Dados de Faíscas irá automaticamente empurrar para baixo cláusulas WHERE válidas para a base de dados. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

A secção PushFilters do plano físico inclui o filtro push Down GreaterThan. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="divisórias":::

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>Ler tabela
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Leia colunas específicas na tabela

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Vistas SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Criar uma visão temporária a partir de um dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Executar consultas contra a vista

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Passos seguintes

Seguem-se artigos adicionais sobre o trabalho com a Azure Cosmos DB Cassandra API da Spark:
 
 * [Operações de upsert](cassandra-spark-upsert-ops.md)
 * [Operações de eliminação](cassandra-spark-delete-ops.md)
 * [Operações de agregação](cassandra-spark-aggregation-ops.md)
 * [Operações de cópia de mesa](cassandra-spark-table-copy-ops.md)

