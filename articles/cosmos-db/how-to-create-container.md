---
title: Criar um contentor no Azure Cosmos DB
description: Aprenda a criar um contentor no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680439"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um contentor do Cosmos do Azure

Este artigo explica as diferentes formas de criar um contentor do Cosmos do Azure (coleção, tabela ou gráfico). Pode utilizar o portal do Azure, CLI do Azure, ou dos SDKs suportados para isso. Este artigo demonstra como criar um contentor, especifique a chave de partição e aprovisionar o débito.

## <a name="create-a-container-using-azure-portal"></a>Criar um contentor com o portal do Azure

### <a id="portal-sql"></a>API DE SQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova coleção**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados ou utilizar um já existente.
   * Introduza um ID de coleção.
   * Introduza uma chave de partição.
   * Introduza um débito aprovisionado (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Painel de captura de ecrã do Explorador de dados, com a nova coleção realçado](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API do Azure Cosmos DB para o MongoDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova coleção**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados ou utilizar um já existente.
   * Introduza um ID de coleção.
   * Introduza uma chave de partição horizontal.
   * Introduza um débito aprovisionado (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã de API do Azure Cosmos DB para o MongoDB, caixa de diálogo Adicionar coleção](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API para Cassandra

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova tabela**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar um keyspace novo, ou utilizar um já existente.
   * Introduza um nome de tabela.
   * Introduza as propriedades e especifique uma chave primária.
   * Introduza um débito aprovisionado (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã do Cassandra API, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Para a API para Cassandra, a chave primária é utilizada como a chave de partição.

### <a id="portal-gremlin"></a>API do Gremlin

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-graph-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **novo gráfico**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados, ou utilizar um já existente.
   * Introduza um ID de gráfico.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma chave de partição para vértices.
   * Introduza um débito aprovisionado (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã da API Gremlin, caixa de diálogo Adicionar gráfico](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de Tabela

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-table-dotnet.md#create-a-database-account), ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione **nova tabela**. Em seguida, fornece os seguintes detalhes:

   * Introduza um ID de tabela.
   * Introduza um débito aprovisionado (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã da API de tabela, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que adicionar uma nova linha.

## <a name="create-a-container-using-azure-cli"></a>Criar um contentor com a CLI do Azure

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

## <a name="create-a-container-using-net-sdk"></a>Criar um contentor com o SDK .NET

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
> Protocolo de MongoDB não compreende o conceito de [unidades de pedido](request-units.md). Para criar uma nova coleção com débito aprovisionado na mesma, utilize o portal do Azure ou SDKs do Cosmos DB para a API de SQL.

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
- [Unidades de pedido no Azure Cosmos DB](request-units.md)
- [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
- [Trabalhar com a conta do Cosmos do Azure](account-overview.md)
