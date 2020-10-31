---
title: Migrar a sua aplicação para utilizar o Azure Cosmos DB Java SDK v4 (com.azure.cosmos)
description: Saiba como atualizar a sua aplicação Java existente de usar os antigos SDKs Azure Cosmos DB Java para o mais recente Java SDK 4.0 (pacote com.azure.cosmos)para Core (SQL) API.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: eb2553346881bc2e8de631988bd11642b85aa847
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099645"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrar a sua aplicação para usar o Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Para mais informações sobre este SDK, por favor veja as notas de lançamento do Azure Cosmos DB Java SDK [v4,](sql-api-sdk-java-v4.md) [repositório Maven,](https://mvnrepository.com/artifact/com.azure/azure-cosmos)Azure Cosmos DB Java SDK v4 dicas de [desempenho](performance-tips-java-sdk-v4-sql.md), e Azure Cosmos DB Java SDK v4 [guia de resolução de problemas](troubleshoot-java-sdk-v4-sql.md).
>

Este artigo explica como atualizar a sua aplicação java existente que está usando um Azure Cosmos DB Java SDK mais antigo para o mais recente Azure Cosmos DB Java SDK 4.0 para Core (SQL) API. Azure Cosmos DB Java SDK v4 corresponde ao `com.azure.cosmos` pacote. Pode utilizar as instruções neste doc se estiver a migrar a sua aplicação a partir de qualquer um dos seguintes SDKs Azure Cosmos DB Java: 

* Sincronização Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK e mapeamentos de pacotes

A tabela a seguir lista diferentes SDKs Azure Cosmos DB Java, o nome do pacote e as informações de lançamento:

| SDK Java| Data da versão: | APIs agregados   | Jarro Maven  | Nome do pacote java  |Referência de API   | Notas de Versão  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x.x  | Junho de 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de lançamento](sql-api-sdk-async-java.md) |
| Sincronização 2.x.x     | Setembro 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de lançamento](sql-api-sdk-java.md)  |
| 3.x.x    | Julho de 2019    | Async(Reator)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | Junho de 2020   | Async(Reator)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Alterações na implementação do nível SDK

Seguem-se as principais diferenças de implementação entre os diferentes SDKs:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava é substituído por reator nas versões Azure Cosmos DB Java SDK 3.x e 4.0

Se não estiver familiarizado com a programação assíncronia ou programação reativa, consulte o [guia de padrão](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) do Reator para uma introdução à programação async e ao Reator do Projeto. Este guia pode ser útil se tiver usado Azure Cosmos DB Sync Java SDK 2.x.x ou Azure Cosmos DB Java SDK 3.x.x Sync API no passado.

Se você tem usado Azure Cosmos DB Async Java SDK 2.x.x, e planeia migrar para o 4.0 SDK, consulte o [Guia reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para obter orientações sobre a conversão do código RxJava para usar o reator.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 tem modo de conectividade direta tanto em APIs async como sync

Se tem usado Azure Cosmos DB Sync Java SDK 2.x.x, note que o modo de ligação direta baseado no TCP (em oposição a HTTP) é implementado em Azure Cosmos DB Java SDK 4.0 para as APIs Async e Sync.

## <a name="api-level-changes"></a>Alterações no nível da API

Seguem-se as alterações de nível API em Azure Cosmos DB Java SDK 4.x.x em comparação com os SDKs anteriores (Java SDK 3.x.x, Async Java SDK 2.x.x e Sync Java SDK 2.x.x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB Java SDK nomeando convenções":::

* O Azure Cosmos DB Java SDK 3.x.x e 4.0 referem os recursos do cliente como `Cosmos<resourceName>` . Por `CosmosClient` exemplo, `CosmosDatabase` . . . `CosmosContainer` . Enquanto na versão 2.x.x, os SDKs Azure Cosmos DB Java não têm um esquema uniforme de nomeação.

* Azure Cosmos DB Java SDK 3.x.x e 4.0 oferecem APIs sync e Async.

  * **Java SDK 4.0** : Todas as classes pertencem à API de Sincronização, a menos que o nome da classe seja anexado `Async` a depois `Cosmos` .

  * **Java SDK 3.x.x:** Todas as classes pertencem à API Async, a menos que o nome da classe seja anexado `Async` a partir de `Cosmos` .

  * **Async Java SDK 2.x.x:** Os nomes da classe são semelhantes a Sync Java SDK 2.x.x, no entanto o nome começa com *Async* .

### <a name="hierarchical-api-structure"></a>Estrutura hierárquica da API

Azure Cosmos DB Java SDK 4.0 e 3.x.x introduz uma estrutura hierárquica da API que organiza os clientes, bases de dados e contentores de forma aninhada, como mostrado no seguinte corte de código 4.0 SDK:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Na versão 2.x.x do Azure Cosmos DB Java SDK, todas as operações sobre recursos e documentos são realizadas através da instância do cliente.

### <a name="representing-documents"></a>Representando documentos

Em Azure Cosmos DB Java SDK 4.0, pojo personalizado e `JsonNodes` são as duas opções para ler e escrever os documentos de Azure Cosmos DB.

No Azure Cosmos DB Java SDK 3.x.x, o `CosmosItemProperties` objeto é exposto pela API pública e servido como representação documental. Esta classe já não está exposta publicamente na versão 4.0.

### <a name="imports"></a>Importações

* Os pacotes Azure Cosmos DB Java SDK 4.0 começam com `com.azure.cosmos`
  * Azure Cosmos DB Java SDK 3.x.x `com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4.0 coloca várias aulas num pacote aninhado `com.azure.cosmos.models` . Alguns destes pacotes incluem:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Os analógicos da API assínc para todos os pacotes acima
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... etc.

### <a name="accessors"></a>Acessórios

Azure Cosmos DB Java SDK 4.0 expõe `get` e `set` métodos para aceder aos membros do caso. Por exemplo, o `CosmosContainer` exemplo tem `container.getId()` e `container.setId()` métodos.

Isto é diferente de Azure Cosmos DB Java SDK 3.x.x que expõe uma interface fluente. Por exemplo, um `CosmosSyncContainer` exemplo que está sobrecarregado para obter ou definir o `container.id()` `id` valor.

## <a name="code-snippet-comparisons"></a>Comparações de corte de código

### <a name="create-resources"></a>Criar recursos

O seguinte corte de código mostra as diferenças na forma como os recursos são criados entre as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Operações de artigos

O seguinte corte de código mostra as diferenças na forma como as operações dos artigos são realizadas entre as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indexação

O seguinte corte de código mostra as diferenças na forma como a indexação é criada entre as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Procedimentos armazenados

O seguinte corte de código mostra as diferenças na forma como os procedimentos armazenados são criados entre as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Feed de alterações

O seguinte corte de código mostra as diferenças na forma como as operações de alimentação de mudança são executadas entre as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Nível de tempo de vida do contentor (TTL)

O seguinte corte de código mostra as diferenças na forma de criar tempo para viver para os dados no recipiente utilizando as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Nível de item Tempo-a-Vida (TTL)

O seguinte corte de código mostra as diferenças de como criar tempo para viver para um item usando as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação Java](create-sql-api-java.md) para gerir dados API AZure Cosmos DB SQL usando o V4 SDK
* Conheça os [SDKs Java baseados no reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Saiba mais sobre a conversão do código assínc rxJava para código async reator com o [Reator vs RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)
