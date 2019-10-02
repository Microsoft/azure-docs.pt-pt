---
title: Criar um contentor no Azure Cosmos DB
description: Aprenda a criar um contentor no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 9805ff9aa4932c262db13c47fd2e442b3d3d676f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811733"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um contêiner Cosmos do Azure

Este artigo explica as diferentes maneiras de criar um contêiner Cosmos do Azure (coleção, tabela ou grafo). Você pode usar portal do Azure, CLI do Azure ou SDKs com suporte para isso. Este artigo demonstra como criar um contêiner, especificar a chave de partição e provisionar a taxa de transferência.

## <a name="create-a-container-using-azure-portal"></a>Criar um contentor com o portal do Azure

### <a id="portal-sql"></a>API DO SQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **novo contêiner**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo banco de dados ou usando um existente.
   * Insira uma ID de contêiner.
   * Insira uma chave de partição.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela do painel de Data Explorer, com o novo contêiner realçado](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API de Azure Cosmos DB para MongoDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **novo contêiner**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo banco de dados ou usando um existente.
   * Insira uma ID de contêiner.
   * Insira uma chave de fragmentação.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API Azure Cosmos DB para MongoDB, caixa de diálogo Adicionar contêiner](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API para Cassandra

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **nova tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo espaço de keyspace ou usando um existente.
   * Introduza um nome de tabela.
   * Insira as propriedades e especifique uma chave primária.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela de API do Cassandra, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Para a API para Cassandra, a chave primária é utilizada como a chave de partição.

### <a id="portal-gremlin"></a>API do Gremlin

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-graph-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel de **Data Explorer** e selecione **novo grafo**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo banco de dados ou usando um existente.
   * Insira uma ID do grafo.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Insira uma chave de partição para vértices.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API do Gremlin, caixa de diálogo Adicionar grafo](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de Tabela

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-table-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **nova tabela**. Em seguida, forneça os seguintes detalhes:

   * Insira uma ID de tabela.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela de API de Tabela, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que adicionar uma nova linha.

## Criar um contêiner usando CLI do Azure<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Os links a seguir mostram como criar recursos de contêiner para Azure Cosmos DB usando CLI do Azure.

Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [API do SQL](cli-samples.md), [API do Cassandra](cli-samples-cassandra.md), [API do MongoDB](cli-samples-mongodb.md), [API do Gremlin](cli-samples-gremlin.md)e [API de tabela](cli-samples-table.md)

* [Criar um contêiner com CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar uma coleção para Azure Cosmos DB para a API do MongoDB com CLI do Azure](/scripts/cli/mongodb/create.md)
* [Criar uma tabela Cassandra com CLI do Azure](/scripts/cli/cassandra/create.md)
* [Criar um grafo Gremlin com CLI do Azure](/scripts/cli/gremlin/create.md)
* [Criar uma tabela de API de Tabela com CLI do Azure](/scripts/cli/table/create.md)

## Criar um contêiner usando o<a id="ps-sql"></a>PowerShell @no__t-<a id="ps-cassandra"></a>1 <a id="ps-gremlin"><a id="ps-table"></a>

Os links a seguir mostram como criar recursos de contêiner para Azure Cosmos DB usando o PowerShell.

Para obter uma lista de todos os exemplos de CLI do Azure em todas as APIs de Azure Cosmos DB, consulte [API do SQL](powershell-samples-sql.md), [API do Cassandra](powershell-samples-cassandra.md), [API do MongoDB](powershell-samples-mongodb.md), [API do Gremlin](powershell-samples-gremlin.md)e [API de tabela](powershell-samples-table.md)

* [Criar um contêiner com o PowerShell](manage-with-powershell.md#create-container)
* [Criar uma coleção para o Azure Cosmos DB para a API do MongoDB com o PowerShell](/scripts/powershell/mongodb/ps-mongodb-create.md)
* [Criar uma tabela Cassandra com o PowerShell](/scripts/powershell/cassandra/ps-cassandra-create.md)
* [Criar um grafo do Gremlin com o PowerShell](/scripts/powershell/gremlin/ps-gremlin-create.md)
* [Criar uma tabela de API de Tabela com o PowerShell](/scripts/powershell/table/ps-table-create.md)

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

### <a id="dotnet-mongodb"></a>API de Azure Cosmos DB para MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de transmissão do MongoDB não entende o conceito de [unidades de solicitação](request-units.md). Para criar uma nova coleção com taxa de transferência provisionada, use o portal do Azure ou Cosmos DB SDKs para a API do SQL.

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Trabalhar com a conta do Azure Cosmos](account-overview.md)
