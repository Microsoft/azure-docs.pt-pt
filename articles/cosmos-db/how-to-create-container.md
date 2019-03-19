---
title: Criar um contentor no Azure Cosmos DB
description: Aprenda a criar um contentor no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: a509710b5d67aefc599b7e7250754aadd7ad9587
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113168"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um contentor do Cosmos do Azure

Este artigo explica as diferentes formas de criar um contentor (coleção, tabela ou gráfico). Utilize o portal do Azure, a CLI do Azure ou SDKs suportados. Este artigo demonstra como criar um contentor, especifique a chave de partição e aprovisionar o débito.

## <a name="create-a-container-by-using-azure-portal"></a>Criar um contentor com o portal do Azure

### <a id="portal-sql"></a>API DE SQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova coleção**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados ou utilizar um já existente.
   * Introduza um ID de coleção.
   * Introduza uma chave de partição.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Painel de captura de ecrã do Explorador de dados, com a nova coleção realçado](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API do Azure Cosmos DB para o MongoDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-mongodb-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova coleção**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados ou utilizar um já existente.
   * Introduza um ID de coleção.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma chave de partição horizontal.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã de API do Azure Cosmos DB para o MongoDB, caixa de diálogo Adicionar coleção](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API para Cassandra

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova tabela**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar um keyspace novo, ou utilizar um já existente.
   * Introduza um nome de tabela.
   * Introduza as propriedades e especifique uma chave primária.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã do Cassandra API, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Para a API para Cassandra, a chave primária é utilizada como a chave de partição.

### <a id="portal-gremlin"></a>API do Gremlin

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-graph-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **novo gráfico**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados, ou utilizar um já existente.
   * Introduza um ID de gráfico.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma chave de partição para vértices.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã da API Gremlin, caixa de diálogo Adicionar gráfico](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de Tabela

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos DB](create-table-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova tabela**. Em seguida, fornece os seguintes detalhes:

   * Introduza um ID de tabela.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã da API de tabela, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que adicionar uma nova linha.

## <a name="create-a-container-by-using-azure-cli"></a>Criar um contentor com a CLI do Azure

### <a id="cli-sql"></a>API DE SQL

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API do Azure Cosmos DB para o MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API para Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>API do Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>API de Tabela

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-by-using-net-sdk"></a>Criar um contentor com o .NET SDK

### <a id="dotnet-sql-graph"></a>API de SQL e API do Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API do Azure Cosmos DB para o MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protocolo de MongoDB não unidades de pedido. Para criar uma nova coleção com débito, utilize o portal do Azure ou a API de SQL.

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passos Seguintes

- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
