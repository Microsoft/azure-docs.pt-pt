---
title: Como chamar procedimentos armazenados, gatilhos e funções definidas pelo usuário usando SDKs de Azure Cosmos DB
description: Saiba como registrar e chamar procedimentos armazenados, gatilhos e funções definidas pelo usuário usando os SDKs de Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360392"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como registrar e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB

A API do SQL no Azure Cosmos DB dá suporte ao registro e à invocação de procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário) escritas em JavaScript. Você pode usar o [.net](sql-api-sdk-dotnet.md)da API do SQL, [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [node. js](sql-api-sdk-node.md)ou SDKs do [Python](sql-api-sdk-python.md) para registrar e invocar os procedimentos armazenados. Depois de definir um ou mais procedimentos armazenados, gatilhos e funções definidas pelo usuário, você pode carregá-los e exibi-los no [portal do Azure](https://portal.azure.com/) usando data Explorer.

## <a id="stored-procedures"></a>Como executar procedimentos armazenados

Os procedimentos armazenados são escritos usando JavaScript. Eles podem criar, atualizar, ler, consultar e excluir itens em um contêiner Cosmos do Azure. Para obter mais informações sobre como escrever procedimentos armazenados no Azure Cosmos DB, consulte [como escrever procedimentos armazenados no artigo Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) .

Os exemplos a seguir mostram como registrar e chamar um procedimento armazenado usando os SDKs de Azure Cosmos DB. Consulte [criar um documento](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) , pois a origem desse procedimento armazenado é salva como `spCreateToDoItem.js`.

> [!NOTE]
> Para contêineres particionados, ao executar um procedimento armazenado, um valor de chave de partição deve ser fornecido nas opções de solicitação. Os procedimentos armazenados sempre estão no escopo de uma chave de partição. Os itens que têm um valor de chave de partição diferente não serão visíveis para o procedimento armazenado. Isso também se aplica a gatilhos.

### <a name="stored-procedures---net-sdk"></a>Procedimentos armazenados-SDK do .NET

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do .NET:

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

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do .NET:

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

### <a name="stored-procedures---java-sdk"></a>Procedimentos armazenados-SDK do Java

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do Java:

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

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do Java:

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

### <a name="stored-procedures---javascript-sdk"></a>Procedimentos armazenados-SDK do JavaScript

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do JavaScript:

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

### <a name="stored-procedures---python-sdk"></a>Procedimentos armazenados-SDK do Python

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do Python

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

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do Python

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

Os exemplos a seguir mostram como registrar e chamar um pré-gatilho usando os SDKs de Azure Cosmos DB. Consulte o [exemplo de pré-disparo](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) , pois a origem para esse pré-gatilho é salva `trgPreValidateToDoItemTimestamp.js`como.

Durante a execução, os pré-gatilhos são passados no objeto requestoptions especificando `PreTriggerInclude` e, em seguida, passando o nome do gatilho em um objeto List.

> [!NOTE]
> Embora o nome do gatilho seja passado como uma lista, você ainda pode executar apenas um gatilho por operação.

### <a name="pre-triggers---net-sdk"></a>Pré-gatilhos-SDK do .NET

O código a seguir mostra como registrar um pré-gatilho usando o SDK do .NET:

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

O código a seguir mostra como chamar um pré-gatilho usando o SDK do .NET:

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

### <a name="pre-triggers---java-sdk"></a>Pré-gatilhos-SDK do Java

O código a seguir mostra como registrar um pré-gatilho usando o SDK do Java:

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

O código a seguir mostra como chamar um pré-gatilho usando o SDK do Java:

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

### <a name="pre-triggers---javascript-sdk"></a>Pré-gatilhos-SDK do JavaScript

O código a seguir mostra como registrar um pré-gatilho usando o SDK do JavaScript:

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

O código a seguir mostra como chamar um pré-gatilho usando o SDK do JavaScript:

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

### <a name="pre-triggers---python-sdk"></a>Pré-gatilhos – SDK do Python

O código a seguir mostra como registrar um pré-gatilho usando o SDK do Python:

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

O código a seguir mostra como chamar um pré-gatilho usando o SDK do Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Como executar pós-gatilhos

Os exemplos a seguir mostram como registrar um post-Trigger usando o Azure Cosmos DB SDKs. Consulte o [exemplo de pós-gatilho](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) , pois a origem para esse pós-gatilho é salva `trgPostUpdateMetadata.js`como.

### <a name="post-triggers---net-sdk"></a>Pós-gatilhos-SDK do .NET

O código a seguir mostra como registrar um post-Trigger usando o SDK do .NET:

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

O código a seguir mostra como chamar um post-Trigger usando o SDK do .NET:

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

### <a name="post-triggers---java-sdk"></a>Pós-gatilhos-SDK do Java

O código a seguir mostra como registrar um post-Trigger usando o SDK do Java:

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

O código a seguir mostra como chamar um post-Trigger usando o SDK do Java:

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

### <a name="post-triggers---javascript-sdk"></a>Pós-gatilhos-SDK do JavaScript

O código a seguir mostra como registrar um post-Trigger usando o SDK do JavaScript:

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

O código a seguir mostra como chamar um post-Trigger usando o SDK do JavaScript:

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

### <a name="post-triggers---python-sdk"></a>Pós-gatilhos-SDK do Python

O código a seguir mostra como registrar um post-Trigger usando o SDK do Python:

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

O código a seguir mostra como chamar um post-Trigger usando o SDK do Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Como trabalhar com funções definidas pelo usuário

Os exemplos a seguir mostram como registrar uma função definida pelo usuário usando os SDKs de Azure Cosmos DB. Consulte este [exemplo de função definida pelo usuário](how-to-write-stored-procedures-triggers-udfs.md#udfs) , pois a origem para esse pós-gatilho é salva `udfTax.js`como.

### <a name="user-defined-functions---net-sdk"></a>Funções definidas pelo usuário – SDK do .NET

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do .NET:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do .NET:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Funções definidas pelo usuário – SDK do Java

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do Java:

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

### <a name="user-defined-functions---javascript-sdk"></a>Funções definidas pelo usuário – SDK do JavaScript

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funções definidas pelo usuário – SDK do Python

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do Python:

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

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do Python:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os conceitos e como escrever ou usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

- [Trabalhando com Azure Cosmos DB procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Trabalhando com API de consulta integrada de linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md)
- [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript no Azure Cosmos DB](how-to-write-javascript-query-api.md)
