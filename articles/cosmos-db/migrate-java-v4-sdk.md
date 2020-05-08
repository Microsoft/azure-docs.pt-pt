---
title: Migrar a sua aplicação para utilizar o Azure Cosmos DB Java SDK v4 (com.azure.cosmos)
description: Saiba como atualizar a sua aplicação Java existente desde a utilização dos sDKs mais antigos do Azure Cosmos DB Java para o mais recente Java SDK 4.0 (pacote com.azure.cosmos)para Core (SQL) API.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984710"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrar a sua aplicação para utilizar o Azure Cosmos DB Java SDK v4

> [!IMPORTANT]  
> Para mais informações sobre este SDK, consulte as notas de lançamento do Azure Cosmos DB Java SDK v4, [o repositório Maven,](https://mvnrepository.com/artifact/com.azure/azure-cosmos) [as dicas](performance-tips-java-sdk-v4-sql.md)de desempenho do Azure Cosmos DB Java SDK v4 e o guia de resolução de [problemas](troubleshoot-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.
>

Este artigo explica como atualizar a sua aplicação Java existente que está a usar um Azure Cosmos DB Java SDK mais antigo para o mais recente Azure Cosmos DB Java SDK 4.0 para Core (SQL) API. Azure Cosmos DB Java SDK v4 corresponde ao `com.azure.cosmos` pacote. Pode utilizar as instruções neste doc se estiver a migrar a sua aplicação de qualquer um dos seguintes SDKs Azure Cosmos DB Java: 

* Sync Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK's e mapeamento de pacotes

A tabela seguinte lista diferentes Azure Cosmos DB Java SDKs, o nome do pacote e as informações de lançamento:

| SDK Java| Data de Lançamento | APIs agregados   | Maven Jar  | Nome do pacote Java  |Referência da API   | Release Notes (Notas de Lançamento)  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x.x  | junho de 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de lançamento](sql-api-sdk-async-java.md) |
| Sincronização 2.x.x     | Setembro de 2018    | Sync   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de lançamento](sql-api-sdk-java.md)  |
| 3.x.x    | Julho de 2019    | Async (Reator)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Abril de 2020   | Async (Reator)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Alterações na implementação do nível SDK

Seguem-se as principais diferenças de implementação entre os diferentes SDKs:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava é substituído por reator nas versões Azure Cosmos DB Java SDK 3.x.x e 4.0

Se não estiver familiarizado com a programação assíncrona ou a Programação Reativa, consulte o guia de [padrão do Reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) para uma introdução à programação assinizada e ao Reator do Projeto. Este guia pode ser útil se tiver usado O Azure Cosmos DB Sync Java SDK 2.x ou o Azure Cosmos DB Java SDK 3.x.x Sync API no passado.

Se tiver usado o Azure Cosmos DB Async Java SDK 2.x.x e planeia migrar para o 4.0 SDK, consulte o [Reator vs RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para obter orientações sobre a conversão do código RxJava para utilizar o Reator.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 tem modo de conectividade direta tanto em Async como em SYNC APIs

Se tiver usado o Azure Cosmos DB Sync Java SDK 2.x.x, note que o modo de ligação direta baseado no TCP (em oposição ao HTTP) é implementado em Azure Cosmos DB Java SDK 4.0 para as APIs Async e Sync.

## <a name="api-level-changes"></a>Alterações no nível da API

Seguem-se as alterações de nível API em Azure Cosmos DB Java SDK 4.x.x em comparação com os SDKs anteriores (Java SDK 3.x.x, Async Java SDK 2.x, e Sync Java SDK 2.x.x):

![Convenções de nomeação Azure Cosmos DB Java SDK](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* O Azure Cosmos DB Java SDK 3.x e 4.0 remetem os recursos do cliente como `Cosmos<resourceName>`. Por `CosmosClient`exemplo, `CosmosDatabase` `CosmosContainer`. . Enquanto na versão 2.x.x, os SDKs Azure Cosmos DB Java não têm um esquema uniforme de nomeação.

* Azure Cosmos DB Java SDK 3.x.x e 4.0 oferecem APIs Sync e Async.

  * **Java SDK 4.0** : Todas as classes pertencem à Sync API a menos que o nome da classe seja anexado com `Async` seguinte `Cosmos`.

  * **Java SDK 3.x:** Todas as classes pertencem à API Async, `Async` a `Cosmos`menos que o nome da classe seja anexado após .

  * **Async Java SDK 2.x:** Os nomes de classe são semelhantes ao Sync Java SDK 2.x, no entanto o nome começa com *Async*.

### <a name="hierarchical-api-structure"></a>Estrutura hierárquica da API

Azure Cosmos DB Java SDK 4.0 e 3.x.x introduzem uma estrutura hierárquica de API que organiza os clientes, bases de dados e contentores de forma aninhada, como mostra o seguinte corte de código 4.0 SDK:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

Na versão 2.x.x do Azure Cosmos DB Java SDK, todas as operações em recursos e documentos são realizadas através da instância de cliente.

### <a name="representing-documents"></a>Documentos representativos

Em Azure Cosmos DB Java SDK 4.0, `JsonNodes` POJO's personalizados e são as duas opções para ler e escrever os documentos da Azure Cosmos DB.

No Azure Cosmos DB Java SDK 3.x,x, o `CosmosItemProperties` objeto é exposto pela API pública e serviu como representação documental. Esta classe já não é exposta publicamente na versão 4.0.

### <a name="imports"></a>Importações

* Os pacotes Azure Cosmos DB Java SDK 4.0 começam com`com.azure.cosmos`
  * Pacotes Azure Cosmos DB Java SDK 3.x.x começam com pacotes`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4.0 coloca `com.azure.cosmos.models`várias classes num pacote aninhado. Alguns destes pacotes incluem:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Os analógicos ASYNC API para todos os pacotes acima referidos
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... etc.

### <a name="accessors"></a>Acessórios

Azure Cosmos DB Java SDK `get` 4.0 expõe e `set` métodos de acesso aos membros da instância. Por exemplo, `CosmosContainer` o `container.getId()` `container.setId()` exemplo tem e métodos.

Isto é diferente do Azure Cosmos DB Java SDK 3.x.x que expõe uma interface fluente. Por exemplo, `CosmosSyncContainer` um `container.id()` exemplo tem que está `id` sobrecarregado para obter ou definir o valor.

## <a name="code-snippet-comparisons"></a>Comparações de códigos

### <a name="create-resources"></a>Criar recursos

O seguinte código de snippet mostra as diferenças na forma como os recursos são criados entre as APIs 4.0 e 3.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
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

### <a name="item-operations"></a>Operações de item

O seguinte código de corte mostra as diferenças na forma como as operações de item são realizadas entre as APIs 4.0 e 3.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

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

O seguinte código de corte mostra as diferenças na forma como a indexação é criada entre as APIs 4.0 e 3.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

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

O seguinte código de corte mostra as diferenças na forma como os procedimentos armazenados são criados entre as APIs 4.0 e 3.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

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
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

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

O seguinte código de corte mostra as diferenças na forma como as operações de alimentação de mudança são executadas entre as APIs 4.0 e 3.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
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

### <a name="container-level-time-to-livettl"></a>Nível de contentores Tempo-A-Viver (TTL)

O seguinte código de corte mostra as diferenças de como criar tempo para viver para dados no recipiente utilizando as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Nível de item Time-To-Live (TTL)

O seguinte código de corte mostra as diferenças de como criar tempo para viver para um item usando as APIs 4.0 e 3.x.x Async:

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

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

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

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

* [Construa uma aplicação Java](create-sql-api-java.md) para gerir os dados da API Azure Cosmos DB SQL utilizando o V4 SDK
* Conheça os [SDKs Java baseados no reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Saiba converter o código de async RxJava para o código assync reator com o [Guia Reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)