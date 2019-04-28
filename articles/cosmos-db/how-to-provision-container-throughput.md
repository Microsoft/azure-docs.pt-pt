---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Aprenda a aprovisionar débito ao nível do contentor no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d092844fbd75fc2307cc13ec9b7779ae9237535d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054643"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Débito de aprovisionar num contentor do Cosmos do Azure

Este artigo explica como aprovisionar o débito num contentor (gráfico ou uma coleção de tabela) no Azure Cosmos DB. Pode aprovisionar o débito num contentor único, ou [débito de aprovisionar uma base de dados](how-to-provision-database-throughput.md) e partilhá-lo entre os contentores na base de dados. Pode aprovisionar o débito num contentor com o portal do Azure, CLI do Azure ou SDKs do Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account), ou selecione uma conta do Cosmos do Azure existente.

1. Abra o **Data Explorer** painel e selecione **nova coleção**. Em seguida, fornece os seguintes detalhes:

   * Indica se estiver a criar uma nova base de dados ou utilizar um já existente.
   * Introduza um ID de contentor (ou tabela ou gráfico).
   * Introduza um valor de chave de partição (por exemplo, `/userid`).
   * Introduza um débito de que pretende aprovisionar (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de ecrã do Data Explorer, com a nova coleção realçado](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Aprovisionar débito com a CLI do Azure

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Se estiver a aprovisionar o débito num contentor numa conta do Azure Cosmos configurado com a API do Azure Cosmos DB para o MongoDB, utilize `/myShardKey` para o caminho da chave de partição. Se estiver a aprovisionar o débito num contentor numa conta do Azure Cosmos configurado com a API de Cassandra, utilize `/myPrimaryKey` para o caminho da chave de partição.

## <a name="provision-throughput-by-using-net-sdk"></a>Débito de aprovisionamento com o .NET SDK

> [!Note]
> Utilize os SDKs do Cosmos para a API de SQL para aprovisionar débito para todas as APIs do DB Cosmos, exceto a Cassandra API.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre o aprovisionamento de débito no Azure Cosmos DB:

* [Como aprovisionar o débito, numa base de dados](how-to-provision-database-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
