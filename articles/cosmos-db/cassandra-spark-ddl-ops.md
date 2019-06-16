---
title: Operações DDL na API de Cassandra do Azure Cosmos DB do Spark
description: Este artigo fornece detalhes sobre as operações DDL keyspace e a tabela de API de Cassandra do Azure Cosmos DB do Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60898887"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operações DDL na API de Cassandra do Azure Cosmos DB do Spark

Este artigo fornece detalhes sobre as operações DDL keyspace e a tabela de API de Cassandra do Azure Cosmos DB do Spark.

## <a name="cassandra-api-related-configuration"></a>Configuração de relacionadas com a API de Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operações Keyspace DDL

### <a name="create-a-keyspace"></a>Criar um keyspace

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o seguinte comando no cqlsh e deverá ver o keyspace que criou anteriormente.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Remover um keyspace

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operações DDL da tabela

**Considerações:**  

- Débito pode ser atribuído ao nível da tabela usando a instrução de tabela de criar.  
- Uma chave de partição pode armazenar 10 GB de dados.  
- Um registo pode armazenar um máximo de 2 MB de dados.  
- Um intervalo de chaves de partição pode armazenar várias chaves de partição.

### <a name="create-a-table"></a>Criar uma tabela

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o seguinte comando no cqlsh e deverá ver a tabela com o nome "livros: 

```bash
USE books_ks;
DESCRIBE books;
```

Valores de TTL predefinido e o débito aprovisionados não são apresentados no resultado do comando anterior, pode obter estes valores a partir do portal.

### <a name="alter-table"></a>Alterar tabela

É possível alterar os seguintes valores utilizando o comando alter table:

* débito aprovisionado 
* valor Time-to-live
<br>Alterações de coluna não são atualmente suportadas.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Remover tabela

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Validar no cqlsh

Execute o seguinte comando no cqlsh e deverá ver que a tabela de "livros" já não está disponível:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Passos Seguintes

Depois de criar o keyspace e a tabela, avance para os seguintes artigos para operações CRUD e muito mais:
 
* [Criar/insert operações](cassandra-spark-create-ops.md)  
* [operações de leitura](cassandra-spark-read-ops.md)  
* [Operações de Upsert](cassandra-spark-upsert-ops.md)  
* [Operações de eliminação](cassandra-spark-delete-ops.md)  
* [Operações de agregação](cassandra-spark-aggregation-ops.md)  
* [Operações de cópia da tabela](cassandra-spark-table-copy-ops.md)  
