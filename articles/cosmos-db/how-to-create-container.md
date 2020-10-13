---
title: Criar um contentor no Azure Cosmos DB
description: Aprenda a criar um recipiente em Azure Cosmos DB utilizando o portal Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020002"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um recipiente Azure Cosmos

Este artigo explica as diferentes formas de criar um recipiente Azure Cosmos (coleção, mesa ou gráfico) usando o portal Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-a-container-using-azure-portal"></a>Criar um contentor com o portal do Azure

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente.

1. Abra o painel **do Explorador de Dados** e selecione **Novo Recipiente**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza a identificação do contentor.
   * Introduza uma chave de partição.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API do Azure Cosmos DB para MongoDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Explorador de Dados** e selecione **Novo Recipiente**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza a identificação do contentor.
   * Introduza uma chave de fragmentos.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>API de Cassandra

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-cassandra-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Nova **Tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar um novo espaço-chave ou a utilizar um existente.
   * Introduza um nome de tabela.
   * Introduza as propriedades e especifique uma chave primária.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

> [!NOTE]
> Para a API para Cassandra, a chave primária é utilizada como a chave de partição.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>API do Gremlin

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-graph-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Novo **Gráfico**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza um ID de gráfico.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma chave de partição para vértices.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

### <a name="table-api"></a><a id="portal-table"></a>API de Tabela

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-table-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Nova **Tabela**. Em seguida, forneça os seguintes detalhes:

   * Introduza uma identificação de mesa.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que adicionar uma nova linha.

## <a name="create-a-container-using-azure-cli"></a>Criar um recipiente usando O Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Os links abaixo mostram como criar recursos de contentores para a Azure Cosmos DB usando Azure CLI.

Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

* [Criar um recipiente com Azure CLI](manage-with-cli.md#create-a-container)
* [Criar uma coleção para Azure Cosmos DB para MongoDB API com Azure CLI](./scripts/cli/mongodb/create.md)
* [Criar uma tabela Cassandra com Azure CLI](./scripts/cli/cassandra/create.md)
* [Crie um gráfico Gremlin com Azure CLI](./scripts/cli/gremlin/create.md)
* [Criar uma tabela API de tabela com CLI Azure](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Criar um recipiente utilizando o PowerShell

Os links abaixo mostram como criar recursos de contentores para a Azure Cosmos DB usando PowerShell.

Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

* [Criar um recipiente com PowerShell](manage-with-powershell.md#create-container)
* [Criar uma coleção para Azure Cosmos DB para MongoDB API com PowerShell](./scripts/powershell/mongodb/create.md)
* [Criar uma tabela Cassandra com PowerShell](./scripts/powershell/cassandra/create.md)
* [Criar um gráfico Gremlin com PowerShell](./scripts/powershell/gremlin/create.md)
* [Criar uma tabela API de tabela com PowerShell](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Criar um contentor com o SDK .NET

Se encontrar exceção no tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com sucesso. A operação de leitura abre uma exceção até que a operação de criação de recolha seja bem sucedida. Para a lista de códigos de estado suportados pela operação de criação consulte os Códigos de Estado HTTP para o artigo [DB do Azure Cosmos.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>API de SQL e API do Gremlin

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API do Azure Cosmos DB para MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de fio MongoDB não compreende o conceito de [Unidades](request-units.md)de Pedido . Para criar uma nova coleção com produção a fortalhada, utilize o portal Azure ou Cosmos DB SDKs para API SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API de Cassandra

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
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](account-overview.md)
