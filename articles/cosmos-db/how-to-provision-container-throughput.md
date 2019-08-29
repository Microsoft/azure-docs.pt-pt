---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Aprenda a aprovisionar débito ao nível do contentor no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: 0975fe5135bbe9f5e1dc65ee0444cc3aab986a2e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093044"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionar a taxa de transferência em um contêiner Cosmos do Azure

Este artigo explica como provisionar a taxa de transferência em um contêiner (coleção, gráfico ou tabela) no Azure Cosmos DB. Você pode provisionar a produtividade em um único contêiner ou provisionar a [taxa de transferência em um banco de dados](how-to-provision-database-throughput.md) e compartilhá-lo entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência em um contêiner usando os SDKs portal do Azure, CLI do Azure ou Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Aprovisionar débito com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente do Azure Cosmos.

1. Abra o painel **Data Explorer** e selecione **nova coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo banco de dados ou usando um existente.
   * Insira uma ID de contêiner (ou tabela ou gráfico).
   * Insira um valor de chave de partição (por `/userid`exemplo,).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de tela de Data Explorer, com a nova coleção realçada](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Aprovisionar débito com a CLI do Azure

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>Provisionar taxa de transferência usando o PowerShell

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

Se você estiver Provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a `/myShardKey` API do Azure Cosmos DB para MongoDB, use para o caminho da chave de partição. Se você estiver Provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com API do Cassandra, use `/myPrimaryKey` para o caminho de chave de partição.

## <a name="provision-throughput-by-using-net-sdk"></a>Provisionar taxa de transferência usando o SDK do .NET

> [!Note]
> Use os SDKs do cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs de Cosmos DB, exceto API do Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela
### <a name="net-v2-sdk"></a>SDK do .net v2

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

### <a name="net-v3-sdk"></a>SDK do .net v3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>API para Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>Passos seguintes

Consulte os artigos a seguir para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
