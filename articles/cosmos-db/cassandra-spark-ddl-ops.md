---
title: Operações da DDL no Azure Cosmos DB Cassandra API da Spark
description: Este artigo detalha as operações do Keyspace e da tabela DDL contra a Azure Cosmos DB Cassandra API da Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c0df05eff5dc84ef24e1ed5afcaf705d99f447ef
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622566"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operações da DDL no Azure Cosmos DB Cassandra API da Spark

Este artigo detalha as operações do Keyspace e da tabela DDL contra a Azure Cosmos DB Cassandra API da Spark.

## <a name="cassandra-api-related-configuration"></a>Configuração relacionada com a API de Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operações DDL do keyspace

### <a name="create-a-keyspace"></a>Criar um espaço de chave

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

Execute o seguinte comando em cqlsh e você deve ver o espaço chave que criou anteriormente.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Largue um espaço de chave

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operações dDL de tabela

**Considerações:**  

- A entrada pode ser atribuída ao nível da tabela utilizando a declaração de criação da tabela.  
- Uma chave de partição pode armazenar 20 GB de dados.  
- Um registo pode armazenar um máximo de 2 MB de dados.  
- Uma gama de chaves de divisória pode armazenar várias teclas de partição.

### <a name="create-a-table"></a>Criar uma tabela

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

Execute o seguinte comando em cqlsh e você deve ver a tabela chamada "livros: 

```bash
USE books_ks;
DESCRIBE books;
```

Os valores de entrada e TTL predefinidos não são mostrados na saída do comando anterior, podendo obter estes valores a partir do portal.

### <a name="alter-table"></a>Alterar mesa

Pode alterar os seguintes valores utilizando o comando da tabela de alterações:

* provisão de entrada 
* valor tempo-a-viver
<br>Atualmente, as alterações das colunas não são suportadas.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Mesa de lançamento

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

Execute o seguinte comando em cqlsh e deve ver que a tabela "livros" já não está disponível:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o espaço-chave e a tabela, proceda aos seguintes artigos para operações crud e mais:
 
* [Criar/inserir operações](cassandra-spark-create-ops.md)  
* [Ler operações](cassandra-spark-read-ops.md)  
* [Operações upsert](cassandra-spark-upsert-ops.md)  
* [Eliminar operações](cassandra-spark-delete-ops.md)  
* [Operações de agregação](cassandra-spark-aggregation-ops.md)  
* [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)  
