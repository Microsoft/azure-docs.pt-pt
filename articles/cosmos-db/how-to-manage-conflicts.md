---
title: Gerir conflitos entre regiões em Azure Cosmos DB
description: Saiba como gerir conflitos em Azure Cosmos DB criando o último escritor-ganha ou uma política de resolução de conflitos personalizada
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f98c2201159350f5774f4d2b05102384f31f3af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339347"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gerir políticas de resolução de conflitos em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Com as gravações multi-regiões, quando vários clientes escrevem para o mesmo item, podem ocorrer conflitos. Quando um conflito ocorre, você pode resolver o conflito usando diferentes políticas de resolução de conflitos. Este artigo descreve como gerir as políticas de resolução de conflitos.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos de última hora

Estas amostras mostram como criar um contentor com uma política de resolução de conflitos de última hora. O caminho padrão para as últimas vitórias é o campo de tempos ou a `_ts` propriedade. Para a API SQL, este também pode ser definido para um caminho definido pelo utilizador com um tipo numérico. Num conflito, o maior valor vence. Se o caminho não estiver definido ou for inválido, é inválido. `_ts` Os conflitos resolvidos com esta política não aparecem na ração de conflitos. Esta política pode ser utilizada por todas as APIs.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionLWWAsync)]

# <a name="sync"></a>[Sincronização](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionLWWSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-lww-javav2"></a>Java V2 SDKs

# <a name="async-java-v2-sdk"></a>[Async Java V2 SDK](#tab/async)

[Async Java V2 SDK](sql-api-sdk-async-java.md) (Maven [com.microsoft.azure::azure-cosmosdb)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sync Java V2 SDK](#tab/sync)

[Sync Java V2 SDK](sql-api-sdk-java.md) (Maven [com.microsoft.azure::azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Criar uma política de resolução de conflitos personalizada usando um procedimento armazenado

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada com um procedimento armazenado para resolver o conflito. Estes conflitos não aparecem na transmissão de conflitos a menos que haja um erro no seu procedimento armazenado. Depois de a apólice ser criada com o recipiente, é necessário criar o procedimento armazenado. A amostra .NET SDK abaixo mostra um exemplo. Esta política é suportada apenas no Core (SQL) Api.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Exemplo de procedimento de resolução de conflitos personalizado

Os procedimentos personalizados de resolução de conflitos devem ser implementados utilizando a assinatura de função abaixo mostrada. O nome da função não necessita de corresponder ao nome utilizado ao registar o procedimento armazenado com o recipiente, mas sim simplifica o nome. Aqui está uma descrição dos parâmetros que devem ser implementados para este procedimento armazenado.

- **incomingItem**: O item que está a ser inserido ou atualizado no compromisso que está a gerar os conflitos. É nulo para apagar operações.
- **existingItem**: O item atualmente comprometido. Este valor não é nulo numa atualização e nulo para uma inserção ou eliminação.
- **isTombstone**: Boolean indicando se o incomingItem está em conflito com um item previamente eliminado. Quando verdadeiro, o existingItem também é nulo.
- **conflituosaItems**: Matriz da versão comprometida de todos os itens no recipiente que estão em conflito com a entradaItem em ID ou quaisquer outras propriedades de índice únicas.

> [!IMPORTANT]
> Tal como em qualquer procedimento armazenado, um procedimento personalizado de resolução de conflitos pode aceder a quaisquer dados com a mesma chave de partição e pode executar qualquer inserção, atualização ou eliminação de operação para resolver conflitos.

Esta amostra armazenada resolve conflitos selecionando o valor mais baixo do `/myCustomId` caminho.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionSprocAsync)]

# <a name="sync"></a>[Sincronização](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionSprocSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav2"></a>Java V2 SDKs

# <a name="async-java-v2-sdk"></a>[Async Java V2 SDK](#tab/async)

[Async Java V2 SDK](sql-api-sdk-async-java.md) (Maven [com.microsoft.azure::azure-cosmosdb)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sync Java V2 SDK](#tab/sync)

[Sync Java V2 SDK](sql-api-sdk-java.md) (Maven [com.microsoft.azure::azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

Após a criação do seu recipiente, deve criar o `resolver` procedimento armazenado.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Após a criação do seu recipiente, deve criar o `resolver` procedimento armazenado.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Após a criação do seu recipiente, deve criar o `resolver` procedimento armazenado.

## <a name="create-a-custom-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos personalizada

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada. Estes conflitos aparecem na ração de conflitos.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionCustomAsync)]

# <a name="sync"></a>[Sincronização](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionCustomSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-javav2"></a>Java V2 SDKs

# <a name="async-java-v2-sdk"></a>[Async Java V2 SDK](#tab/async)

[Async Java V2 SDK](sql-api-sdk-async-java.md) (Maven [com.microsoft.azure::azure-cosmosdb)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sync Java V2 SDK](#tab/sync)

[Sync Java V2 SDK](sql-api-sdk-java.md) (Maven [com.microsoft.azure::azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Ler a partir do feed de conflitos

Estes exemplos mostram como ler a partir do feed de conflitos de um contentor. Os conflitos só aparecem no feed de conflitos se não forem resolvidos automaticamente ou se usarem uma política de conflitos personalizada.

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-v2-sdks"></a><a id="read-from-conflict-feed-javav2"></a>Java V2 SDKs

# <a name="async-java-v2-sdk"></a>[Async Java V2 SDK](#tab/async)

[Async Java V2 SDK](sql-api-sdk-async-java.md) (Maven [com.microsoft.azure::azure-cosmosdb)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="sync-java-v2-sdk"></a>[Sync Java V2 SDK](#tab/sync)

[Sync Java V2 SDK](sql-api-sdk-java.md) (Maven [com.microsoft.azure::azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Passos seguintes

Conheça os seguintes conceitos Azure Cosmos DB:

- [Distribuição global - em segundo plano](global-dist-under-the-hood.md)
- [Como configurar várias regiões escreve nas suas aplicações](how-to-multi-master.md)
- [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Adicione ou remova regiões da sua conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Como configurar a região-região escreve nas suas aplicações](how-to-multi-master.md).
- [Criação de partições e distribuição de dados](partitioning-overview.md)
- [Indexação no Azure Cosmos DB](index-policy.md)
