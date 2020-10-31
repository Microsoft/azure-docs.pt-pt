---
title: Migrar a sua aplicação para utilizar o Azure Cosmos DB .NET SDK 3.0 (com.azure.cosmos)
description: Saiba como atualizar a sua aplicação .NET existente do v2 SDK para o mais recente .NET SDK v3 (pacote com.azure.cosmos) para Core (SQL) API.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 3cddf76ae0157c6729e6f58e40ee3f1725a9d395
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096670"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migrar a sua aplicação para utilizar o Azure Cosmos DB .NET SDK v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Para saber mais sobre o Azure Cosmos DB .NET SDK v3, consulte as [notas de lançamento](sql-api-sdk-dotnet-standard.md), o [repositório .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3), .NET SDK v3 [Performance Tips](performance-tips-dotnet-sdk-v3-sql.md), e o guia [de resolução de problemas](troubleshoot-dot-net-sdk.md).
>

Este artigo destaca algumas das considerações de atualização da sua aplicação .NET existente para o mais recente Azure Cosmos DB .NET SDK v3 para Core (SQL) API. Azure Cosmos DB .NET SDK v3 corresponde ao espaço de nomes Microsoft.Azure.Cosmos. Pode utilizar as informações fornecidas neste doc se estiver a migrar a sua aplicação a partir de qualquer um dos seguintes SDKs Azure Cosmos DB .NET:

* Azure Cosmos DB .NET Framework SDK v2 para API SQL
* Azure Cosmos DB .NET Core SDK v2 para SQL API

As instruções deste artigo também o ajudam a migrar as seguintes bibliotecas externas que agora fazem parte da Azure Cosmos DB .NET SDK v3 para Core (SQL) API:

* .NET alterar biblioteca de processadores de alimentação 2.0
* .NET biblioteca executora a granel 1.1 ou maior

## <a name="whats-new-in-the-net-v3-sdk"></a>Novidades no .NET V3 SDK

O V3 SDK contém muitas melhorias de usabilidade e desempenho, incluindo:

* Nomeação de modelo de programação intuitiva
* .NET Standard 2.0 **
* Aumento do desempenho através do suporte de API em fluxo
* Hierarquia fluente que substitui a necessidade de fábrica URI
* Suporte incorporado para alterar biblioteca de processadores de feed
* Apoio integrado às operações a granel
* Mockabale APIs para testes de unidade mais fáceis
* Lote transacional e suporte blazor
* Serializadores pluggáveis
* Recipientes não divididos e autoescalados

** O SDK visa .NET Standard 2.0 que unifica o atual Azure Cosmos DB .NET Framework e .NET Core SDKs num único .NET SDK. Pode utilizar o .NET SDK em qualquer plataforma que implemente .NET Standard 2.0, incluindo as suas aplicações .NET Framework 4.6.1+ e .NET Core 2.0+.

A maior parte da lógica de networking, redatória e níveis mais baixos do SDK permanecem em grande parte inalterados.

**O Azure Cosmos DB .NET SDK v3 é agora fonte aberta.** Congratulamo-nos com quaisquer pedidos de atração e estaremos a registar problemas e a rastrear feedback no [GitHub.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Trabalharemos em assumir quaisquer funcionalidades que melhorem a experiência do cliente.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Porquê migrar para o .NET v3 SDK

Além das inúmeras melhorias de usabilidade e desempenho, os novos investimentos de recurso realizados nos mais recentes SDK não serão devolvidos às versões mais antigas.

Embora não existam planos [imediatos para retirar o suporte para os 2.0 SDKs,](sql-api-sdk-dotnet.md)os SDKs serão substituídos por versões mais recentes no futuro e o SDK entrará em modo de manutenção. Para uma melhor experiência de desenvolvimento, recomendamos sempre começar com a mais recente versão suportada da SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Principais mudanças de nome de v2 SDK para v3 SDK

Foram aplicadas as seguintes alterações de nome ao longo do .NET 3.0 SDK para alinhar com as convenções de nomeação API para a API Core (SQL):

* `DocumentClient` é renomeado para `CosmosClient`
* `Collection` é renomeado para `Container`
* `Document` é renomeado para `Item`

Todos os objetos de recurso são renomeados com propriedades adicionais, que, inclui o nome do recurso para clareza.

Seguem-se algumas das principais alterações de nome de classe:

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Aulas substituídas em .NET v3 SDK

As seguintes classes foram substituídas no 3.0 SDK:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Os Microsoft.Azure.Documents. A classe UriFactory foi substituída pelo design fluente. O design fluente constrói URLs internamente e permite que um único `Container` objeto seja passado ao contrário de um , e `DocumentClient` `DatabaseName` `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Alterações na geração de ID do item

O item ID já não é auto-povoado no .NET v3 SDK. Portanto, o ID do item deve incluir especificamente um ID gerado. Veja o seguinte exemplo:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Mudou o comportamento padrão para o modo de ligação

O V3 SDK agora desrescumbe aos modos de ligação Direct + TCP em comparação com os anteriores V2 SDK, que predefiniram nos modos de ligação Gateway + HTTPS. Esta alteração proporciona um melhor desempenho e escalabilidade.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Alterações às Opções de Feed (Opções QueryRequest Options em v3.0 SDK)

A `FeedOptions` classe em SDK v2 foi agora renomeada para `QueryRequestOptions` no SDK v3 e dentro da classe, várias propriedades tiveram alterações no nome e/ou valor padrão ou foram removidas completamente.  

`FeedOptions.MaxDegreeOfParallelism` foi renomeado para `QueryRequestOptions.MaxConcurrency` valor padrão e comportamento associado permanece o mesmo, operações executar lado cliente durante a execução de consultas paralelas será executado em série sem paralelo.

`FeedOptions.EnableCrossPartitionQuery` foi removido e o comportamento padrão em SDK 3.0 é que as consultas de partição cruzada serão executadas sem a necessidade de ativar a propriedade especificamente.

`FeedOptions.PopulateQueryMetrics` é ativado por padrão com os resultados presentes na propriedade de diagnóstico da resposta.

`FeedOptions.RequestContinuation` foi agora promovido aos próprios métodos de consulta.

Foram removidas as seguintes propriedades:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Construção de um cliente

O .NET SDK v3 fornece uma classe fluente `CosmosClientBuilder` que substitui a necessidade da Fábrica SDK v2 URI.

O exemplo a seguir cria um novo `CosmosClientBuilder` com uma consistência forte E uma lista de locais preferidos:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Utilizando as APIs do processador de alimentação de alteração diretamente do V3 SDK

O V3 SDK tem suporte incorporado para as APIs do processador change feed, permitindo-lhe usar o mesmo SDK para construir a sua aplicação e alterar a implementação do processador de feed. Anteriormente, tinha de utilizar uma biblioteca separada de processadores de feed de alteração.

Para mais informações, consulte [como migrar da biblioteca do processador de mudanças para o Azure Cosmos DB .NET v3 SDK](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Utilizando a biblioteca de executor a granel diretamente do V3 SDK

O V3 SDK tem suporte incorporado para a biblioteca de executores a granel, permitindo-lhe usar o mesmo SDK para construir a sua aplicação e realizar operações a granel. Anteriormente, era obrigado a utilizar uma biblioteca de executor a granel separada.

Para mais informações, consulte [como migrar da biblioteca de executores a granel para suporte a granel em Azure Cosmos DB .NET V3 SDK](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>Comparações de corte de código

O seguinte código snippet mostra as diferenças na forma como os recursos são criados entre os .NET v2 e v3 SDKs:

## <a name="database-operations"></a>Operações da base de dados

### <a name="create-a-database"></a>Criar uma base de dados

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Leia uma base de dados por ID

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Eliminar uma base de dados

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operações de contentores

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Criar um recipiente (Autoscale + Tempo para viver com expiração)

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Ler propriedades de contentores

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Eliminar um contentor

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operações de item e consulta

### <a name="create-an-item"></a>Criar um item

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Leia todos os itens num recipiente

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Artigos de consulta

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Eliminar um item

# <a name="net-sdk-v3"></a>[SDK .NET v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[SDK .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação de consola](sql-api-get-started.md) para gerir dados API AZure Cosmos DB SQL usando o V3 SDK
* Saiba mais sobre [o que pode fazer com o V3 SDK](sql-api-dotnet-v3sdk-samples.md)
