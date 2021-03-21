---
title: Operações DDL em Azure Cosmos DB Cassandra API da Spark
description: Este artigo detalha as operações de keyspace e tabela DDL contra a Azure Cosmos DB Cassandra API da Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 73d31fff362807937cbd87b8e1313cf601909802
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092182"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operações DDL em Azure Cosmos DB Cassandra API da Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo detalha as operações de keyspace e tabela DDL contra a Azure Cosmos DB Cassandra API da Spark.

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

## <a name="keyspace-ddl-operations"></a>Operações DDL keyspace

### <a name="create-a-keyspace"></a>Criar um espaço-chave

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

Executar o seguinte comando em cqlsh e você deve ver o espaço-chave que criou anteriormente.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Deixe cair um espaço-chave

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operações DDL de tabela

**Considerações:**  

- A produção pode ser atribuída ao nível da mesa utilizando a declaração de tabela de criação.  
- Uma chave de partição pode armazenar 20 GB de dados.  
- Um registo pode armazenar um máximo de 2 MB de dados.  
- Uma gama de chaves de partição pode armazenar várias chaves de partição.

### <a name="create-a-table"></a>Criar uma tabela

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Validar em cqlsh

Executar o seguinte comando em cqlsh e você deve ver a tabela chamada "livros: 

```bash
USE books_ks;
DESCRIBE books;
```

Os valores de TTL de produção e TTL previstos não são apresentados na saída do comando anterior, pode obter estes valores a partir do portal.

### <a name="alter-table"></a>Alterar tabela

Pode alterar os seguintes valores utilizando o comando de tabela alter:

* produção a provisionada 
* valor tempo-a-vida
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

Executar o seguinte comando em cqlsh e você deve ver que a tabela "livros" já não está disponível:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o espaço-chave e a tabela, proceda aos seguintes artigos para operações CRUD e muito mais:
 
* [Criar/inserir operações](cassandra-spark-create-ops.md)  
* [Operações de leitura](cassandra-spark-read-ops.md)  
* [Operações de upsert](cassandra-spark-upsert-ops.md)  
* [Operações de eliminação](cassandra-spark-delete-ops.md)  
* [Operações de agregação](cassandra-spark-aggregation-ops.md)  
* [Operações de cópia de mesa](cassandra-spark-table-copy-ops.md)  
