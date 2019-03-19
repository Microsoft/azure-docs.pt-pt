---
title: Como chamar procedimentos armazenados, acionadores e funções definidas pelo utilizador através de SDKs do Azure Cosmos DB
description: Saiba como registar e chamar procedimentos armazenados, acionadores e funções definidas pelo utilizador com os SDKs do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/08/2018
ms.author: mjbrown
ms.openlocfilehash: d3ab0f78cc59c94a95aac6c067ad185476502f6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998613"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como registar e utilizar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB

A API de SQL no Azure Cosmos DB suporta a registar e invocar procedimentos armazenados, acionadores e definido pelo utilizador funções (UDFs) escritas em JavaScript. Pode usar a API de SQL [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [node. js](sql-api-sdk-node.md), ou [Python](sql-api-sdk-python.md) SDKs para se registrar e invocar os procedimentos armazenados. Depois de definir uma ou mais procedimentos armazenados, acionadores e funções definidas pelo utilizador, pode carregar e visualizá-los no [portal do Azure](https://portal.azure.com/) através do Explorador de dados.

## <a id="stored-procedures"></a>Como executar procedimentos armazenados

Procedimentos armazenados são escritos usando JavaScript. Eles podem criar, atualizar, ler, consulta e eliminar itens dentro de um contentor do Cosmos do Azure. Para obter mais informações sobre como escrever procedimentos armazenados no Azure Cosmos DB, consulte [como escrever procedimentos armazenados no Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) artigo.

Os exemplos seguintes mostram como se registrar e chamar um procedimento armazenado ao utilizar os SDKs do Azure Cosmos DB. Consulte a [criar um documento](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) à medida que a origem para este procedimento armazenado é guardada como `spCreateToDoItem.js`.

> [!NOTE]
> Para contentores particionadas, ao executar um procedimento armazenado, um valor de chave de partição tem de ser fornecido nas opções de pedido. Procedimentos armazenados sempre estão no âmbito de uma chave de partição. Itens que tenham um valor de chave de partição diferente não serão visíveis para o procedimento armazenado. Isso também é aplicado a acionadores também.

### <a name="stored-procedures---net-sdk"></a>Procedimentos armazenados - .NET SDK

O exemplo seguinte mostra como registar um procedimento armazenado com o SDK .NET:

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

O código seguinte mostra como chamar um procedimento armazenado com o SDK .NET:

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
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Procedimentos armazenados - Java SDK

O exemplo seguinte mostra como registar um procedimento armazenado com o SDK Java:

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

O código seguinte mostra como chamar um procedimento armazenado com o SDK Java:

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
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedimentos armazenados - JavaScript SDK

O exemplo seguinte mostra como registar um procedimento armazenado com o SDK de JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

O código seguinte mostra como chamar um procedimento armazenado com o SDK do JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedimentos armazenados - Python SDK

O exemplo seguinte mostra como registar um procedimento armazenado com o SDK de Python

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

O código seguinte mostra como chamar um procedimento armazenado com o SDK de Python

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

## <a id="pre-triggers"></a>Como executar a pré-acionadores

Os exemplos seguintes mostram como se registrar e chamar um pré-acionador de ao utilizar os SDKs do Azure Cosmos DB. Consulte a [exemplo de pré-acionador](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) à medida que a origem para este pré-acionador é guardada como `trgPreValidateToDoItemTimestamp.js`.

Quando em execução, os pré-acionadores de são passados no objeto de RequestOptions especificando `PreTriggerInclude` e, em seguida, passando o nome do acionador de um objeto de lista.

> [!NOTE]
> Mesmo que o nome do acionador é passado como uma lista, ainda pode executar apenas um acionador por operação.

### <a name="pre-triggers---net-sdk"></a>Pré-acionadores de-.NET SDK

O código seguinte mostra como registar-se um pré-acionador de utilizando o SDK .NET:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O código seguinte mostra como chamar um pré-acionador de utilizando o SDK .NET:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Pré-acionadores-Java SDK

O código seguinte mostra como registar-se um pré-acionador de utilizando o SDK Java:

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

O código seguinte mostra como chamar um pré-acionador de utilizando o SDK Java:

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

### <a name="pre-triggers---javascript-sdk"></a>Pré-acionadores-JavaScript SDK

O código seguinte mostra como registar-se um pré-acionador de usando o JavaScript SDK:

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

O código seguinte mostra como chamar um pré-acionador de usando o JavaScript SDK:

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

### <a name="pre-triggers---python-sdk"></a>Pré-acionadores-SDK Python

O código seguinte mostra como registar-se um pré-acionador de utilizando o SDK de Python:

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

O código seguinte mostra como chamar um pré-acionador de utilizando o SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Como executar os pós-acionadores de

Os exemplos seguintes mostram como registar um pós-acionador de com os SDKs do Azure Cosmos DB. Consulte a [exemplo de pós-acionador](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) à medida que a origem para este pós-acionador é guardada como `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Pós-acionadores de-.NET SDK

O código seguinte mostra como registar-se um pós-acionador de utilizando o SDK .NET:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js");,
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O código seguinte mostra como chamar um pós-acionador de utilizando o SDK .NET:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Pós-acionadores de-Java SDK

O código seguinte mostra como registar-se um pós-acionador de utilizando o SDK Java:

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

O código seguinte mostra como chamar um pós-acionador de utilizando o SDK Java:

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

### <a name="post-triggers---javascript-sdk"></a>Pós-acionadores de-JavaScript SDK

O código seguinte mostra como registar-se um pós-acionador de usando o JavaScript SDK:

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

O código seguinte mostra como chamar um pós-acionador de usando o JavaScript SDK:

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

### <a name="post-triggers---python-sdk"></a>Pós-acionadores de-SDK Python

O código seguinte mostra como registar-se um pós-acionador de utilizando o SDK de Python:

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

O código seguinte mostra como chamar um pós-acionador de utilizando o SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Como trabalhar com funções definidas pelo utilizador

Os exemplos seguintes mostram como registar uma função definida pelo utilizador com os SDKs do Azure Cosmos DB. Consulte este [exemplo de função definida pelo utilizador](how-to-write-stored-procedures-triggers-udfs.md#udfs) à medida que a origem para este pós-acionador é guardada como `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Funções definidas pelo utilizador - .NET SDK

O código a seguir mostra como registrar uma função definida pelo utilizador utilizando o SDK .NET:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

O código a seguir mostra como chamar uma função definida pelo utilizador utilizando o SDK .NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Funções definidas pelo utilizador - Java SDK

O código a seguir mostra como registrar uma função definida pelo utilizador utilizando o SDK Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

O código a seguir mostra como chamar uma função definida pelo utilizador utilizando o SDK Java:

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

O código a seguir mostra como registrar uma função definida pelo utilizador através do JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

O código a seguir mostra como chamar uma função definida pelo utilizador através do JavaScript SDK:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funções definidas pelo utilizador - Python SDK

O código a seguir mostra como registrar uma função definida pelo utilizador utilizando o SDK de Python:

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

O código a seguir mostra como chamar uma função definida pelo utilizador utilizando o SDK de Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Passos Seguintes

Saber mais, conceitos e procedimento escrita ou usar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB:

- [Trabalhar com o Azure Cosmos DB procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Trabalhar com a linguagem JavaScript integrada a API de consulta no Azure Cosmos DB](javascript-query-api.md)
- [Como escrever procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Como escrever procedimentos armazenados e acionadores com a API de consulta do Javascript no Azure Cosmos DB](how-to-write-javascript-query-api.md)
