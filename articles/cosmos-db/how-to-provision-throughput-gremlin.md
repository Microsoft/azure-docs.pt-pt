---
title: Produção de provisão sobre os recursos da API AZure Cosmos DB Gremlin
description: Saiba como providenciar contentores, bases de dados e produção de escala automática nos recursos da Azure Cosmos DB Gremlin API. Utilizará o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086164"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Base de dados, contentores ou produção de escala automática nos recursos da API API da Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo explica como provisão para a produção na Azure Cosmos DB Gremlin API. Pode providenciar padrão (manual) ou produção de escala automática num contentor ou numa base de dados e partilhá-la entre os recipientes dentro da base de dados. Você pode provistuar produção usando o portal Azure CLI, ou Azure Cosmos DB SDKs.

Se estiver a utilizar uma API diferente, consulte [a API SQL,](how-to-provision-container-throughput.md) [a API Cassandra,](how-to-provision-throughput-cassandra.md)API para artigos [mongoDB](how-to-provision-throughput-mongodb.md) para a disponibilização do rendimento.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione Novo **Gráfico** . Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente. Selecione a opção **de produção da base de dados De provisionar** se pretender obter a produção no nível da base de dados.
   * Introduza uma identificação de gráfico.
   * Introduza um valor chave de partição (por exemplo, `/ItemID` ).
   * Introduza uma produção que pretende prever (por exemplo, 1000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Screenshot do Data Explorer, ao criar um novo gráfico com produção de nível de base de dados":::

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Utilize os SDKs Cosmos para a SQL API para provisão para todas as APIs Azure Cosmos DB, com exceção de Cassandra e MongoDB API.

### <a name="provision-container-level-throughput"></a>Produção do nível do contentor de provisão

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

### <a name="provision-database-level-throughput"></a>Produção de nível de base de dados de provisão

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Os modelos do Gestor de Recursos Azure podem ser usados para obter rendimento de escala automática na base de dados ou recursos de nível de contentor para todas as APIs DB de Azure Cosmos. Consulte [os modelos do Azure Resource Manager para amostras do Azure Cosmos.](templates-samples-gremlin.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI pode ser usado para provisões de viação de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs Azure Cosmos DB. Para amostras consulte [amostras do Azure CLI para Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell pode ser utilizado para a disponibilização de produção de escala automática numa base de dados ou recursos de nível de contentor para todas as APIs DB do Azure Cosmos. Para amostras consulte [as amostras Azure PowerShell para Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre o fornecimento de produção em Azure Cosmos DB:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)