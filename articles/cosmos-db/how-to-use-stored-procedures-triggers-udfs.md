---
title: Registe e utilize procedimentos, gatilhos e funções definidas pelo utilizador em SDKs DB Azure Cosmos
description: Saiba como registar e ligar procedimentos, gatilhos e funções definidas pelo utilizador utilizando os SDKs DB Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: mjbrown
ms.openlocfilehash: 00740bc2255962089789682e3227ce414fd0ce64
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582505"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como registar e utilizar procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB

A API de SQL no Azure Cosmos DB suporta o registo e a invocação de procedimentos armazenados, acionadores e funções definidas pelo utilizador (UDFs) escritos em linguagem JavaScript. Pode utilizar os SQL API [.NET](sql-api-sdk-dotnet.md), [.NET Core,](sql-api-sdk-dotnet-core.md) [Java,](sql-api-sdk-java.md) [JavaScript,](sql-api-sdk-node.md) [Node.js](sql-api-sdk-node.md)ou [Python](sql-api-sdk-python.md) SDKs para registar e invocar os procedimentos armazenados. Depois de ter definido um ou mais procedimentos armazenados, gatilhos e funções definidas pelo utilizador, pode carregá-los e vê-los no [portal Azure](https://portal.azure.com/) utilizando o Data Explorer.

## <a id="stored-procedures"></a>Como executar procedimentos armazenados

Os procedimentos armazenados são escritos usando javaScript. Podem criar, atualizar, ler, consultar e apagar itens dentro de um contentor Azure Cosmos. Para obter mais informações sobre como escrever procedimentos armazenados em Azure Cosmos DB, consulte [Como escrever procedimentos armazenados no artigo da Azure Cosmos DB.](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)

Os seguintes exemplos mostram como registar e chamar um procedimento armazenado utilizando os SDKs DB Azure Cosmos. Consulte a [Create a Document,](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) uma vez que a fonte deste procedimento armazenado é guardada de acordo com `spCreateToDoItem.js`.

> [!NOTE]
> Para os recipientes divididos, ao executar um procedimento armazenado, deve ser fornecido um valor-chave de partição nas opções de pedido. Os procedimentos armazenados são sempre remetos a uma chave de partição. Os itens que tenham um valor-chave de divisória diferente não serão visíveis ao procedimento armazenado. Isto também se aplica aos gatilhos.

### <a name="stored-procedures---net-sdk-v2"></a>Procedimentos armazenados - .NET SDK V2

O exemplo que se segue mostra como registar um procedimento armazenado utilizando o .NET SDK V2:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

O seguinte código mostra como chamar um procedimento armazenado utilizando o .NET SDK V2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
```

### <a name="stored-procedures---net-sdk-v3"></a>Procedimentos armazenados - .NET SDK V3

O exemplo que se segue mostra como registar um procedimento armazenado utilizando o .NET SDK V3:

```csharp
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("database", "container").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = "spCreateToDoItem",
    Body = File.ReadAllText(@"..\js\spCreateToDoItem.js")
});
```

O seguinte código mostra como chamar um procedimento armazenado utilizando o .NET SDK V3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), newItems);
```

### <a name="stored-procedures---java-sdk"></a>Procedimentos armazenados - Java SDK

O exemplo que se segue mostra como registar um procedimento armazenado utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

O seguinte código mostra como chamar um procedimento armazenado utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedimentos armazenados - JavaScript SDK

O exemplo seguinte mostra como registar um procedimento armazenado utilizando o JavaScript SDK

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

O seguinte código mostra como chamar um procedimento armazenado utilizando o JavaScript SDK:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedimentos armazenados - Python SDK

O exemplo que se segue mostra como registar um procedimento armazenado utilizando o Python SDK

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

O código seguinte mostra como chamar um procedimento armazenado utilizando o Python SDK

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Como executar pré-gatilhos

Os exemplos seguintes mostram como registar e chamar um pré-gatilho utilizando os SDKs DB Azure Cosmos. Consulte o [exemplo do pré-gatilho,](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) uma vez que a fonte para este pré-gatilho é guardada como `trgPreValidateToDoItemTimestamp.js`.

Ao executar, os pré-gatilhos são passados no objeto RequestOptions especificando `PreTriggerInclude` e, em seguida, passando o nome do gatilho num objeto de Lista.

> [!NOTE]
> Mesmo que o nome do gatilho seja passado como Lista, ainda pode executar apenas um gatilho por operação.

### <a name="pre-triggers---net-sdk-v2"></a>Pré-gatilhos - .NET SDK V2

O seguinte código mostra como registar um pré-gatilho utilizando o .NET SDK V2:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O seguinte código mostra como ligar para um pré-gatilho utilizando o .NET SDK V2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Pré-gatilhos - .NET SDK V3

O seguinte código mostra como registar um pré-gatilho utilizando o .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

O seguinte código mostra como ligar para um pré-gatilho utilizando o .NET SDK V3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Pré-gatilhos - Java SDK

O seguinte código mostra como registar um pré-gatilho utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

O seguinte código mostra como chamar um pré-gatilho usando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Pré-gatilhos - JavaScript SDK

O seguinte código mostra como registar um pré-gatilho utilizando o JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

O seguinte código mostra como ligar para um pré-gatilho utilizando o JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Pré-gatilhos - Python SDK

O seguinte código mostra como registar um pré-gatilho utilizando o Python SDK:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

O seguinte código mostra como chamar um pré-gatilho usando o Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Como executar pós-gatilhos

Os exemplos seguintes mostram como registar um pós-gatilho utilizando os SDKs DB Azure Cosmos. Consulte o [exemplo pós-gatilho,](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) uma vez que a fonte para este pós-gatilho é guardada como `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk-v2"></a>Pós-gatilhos - .NET SDK V2

O seguinte código mostra como registar um pós-gatilho utilizando o .NET SDK V2:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O seguinte código mostra como ligar para um pós-gatilho utilizando o .NET SDK V2:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Pós-gatilhos - .NET SDK V3

O seguinte código mostra como registar um pós-gatilho utilizando o .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

O seguinte código mostra como ligar para um pós-gatilho utilizando o .NET SDK V3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Pós-gatilhos - Java SDK

O seguinte código mostra como registar um pós-gatilho utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

O seguinte código mostra como ligar para um pós-gatilho usando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Pós-gatilhos - JavaScript SDK

O seguinte código mostra como registar um pós-gatilho utilizando o JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

O seguinte código mostra como ligar para um pós-gatilho utilizando o JavaScript SDK:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Pós-gatilhos - Python SDK

O seguinte código mostra como registar um pós-gatilho utilizando o Python SDK:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

O seguinte código mostra como ligar para um pós-gatilho usando o Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Como trabalhar com funções definidas pelo utilizador

Os exemplos seguintes mostram como registar uma função definida pelo utilizador utilizando os SDKs DB Azure Cosmos. Consulte este [exemplo de função definido pelo Utilizador](how-to-write-stored-procedures-triggers-udfs.md#udfs) como a fonte para este pós-gatilho é guardada como `udfTax.js`.

### <a name="user-defined-functions---net-sdk-v2"></a>Funções definidas pelo utilizador - .NET SDK V2

O seguinte código mostra como registar uma função definida pelo utilizador utilizando o .NET SDK V2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

O seguinte código mostra como ligar para uma função definida pelo utilizador utilizando o .NET SDK V2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Funções definidas pelo utilizador - .NET SDK V3

O seguinte código mostra como registar uma função definida pelo utilizador utilizando o .NET SDK V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

O seguinte código mostra como ligar para uma função definida pelo utilizador utilizando o .NET SDK V3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Funções definidas pelo utilizador - Java SDK

O seguinte código mostra como registar uma função definida pelo utilizador utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

O seguinte código mostra como ligar para uma função definida pelo utilizador utilizando o Java SDK:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funções definidas pelo utilizador - JavaScript SDK

O seguinte código mostra como registar uma função definida pelo utilizador utilizando o JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

O seguinte código mostra como ligar para uma função definida pelo utilizador utilizando o JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funções definidas pelo utilizador - Python SDK

O seguinte código mostra como registar uma função definida pelo utilizador utilizando o Python SDK:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

O seguinte código mostra como ligar para uma função definida pelo utilizador utilizando o Python SDK:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais conceitos e como escrever ou utilizar procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB:

- [Trabalhar com a Azure Cosmos DB procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Trabalhar com a api integrada de consulta integrada em língua JavaScript em Azure Cosmos DB](javascript-query-api.md)
- [Como escrever procedimentos, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Como escrever procedimentos e gatilhos armazenados usando API Javascript Consulta ry em Azure Cosmos DB](how-to-write-javascript-query-api.md)
