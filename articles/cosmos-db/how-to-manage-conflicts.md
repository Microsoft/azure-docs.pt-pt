---
title: Saiba como gerir conflitos entre regiões no Azure Cosmos DB
description: Saiba como gerir conflitos no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 77fb0e195cee03405c4a601fe8c57d4a2690a4b2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815082"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gerenciar políticas de resolução de conflitos no Azure Cosmos DB

Com gravações de várias regiões, quando vários clientes gravam no mesmo item, podem ocorrer conflitos. Quando ocorrer um conflito, você poderá resolver o conflito usando diferentes políticas de resolução de conflitos. Este artigo descreve como gerenciar políticas de resolução de conflitos.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos do last-writer-WINS

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos do last-writer-WINS. O caminho padrão para last-writer-WINS é o campo timestamp ou a `_ts` propriedade. Para a API do SQL, isso também pode ser definido como um caminho definido pelo usuário com um tipo numérico. Em um conflito, o valor mais alto vence. Se o caminho não for definido ou for inválido, o padrão `_ts`será. Os conflitos resolvidos com essa política não aparecem no feed de conflitos. Essa política pode ser usada por todas as APIs.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>SDK DO .NET V2

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

### <a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>SDK DO .NET V3

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

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>SDK do Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>SDK do Java Sync

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>SDK do Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>SDK do Python

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

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Criar uma política de resolução de conflito personalizada usando um procedimento armazenado

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada com um procedimento armazenado para resolver o conflito. Esses conflitos não aparecem no feed de conflitos, a menos que haja um erro em seu procedimento armazenado. Depois que a política for criada com o contêiner, você precisará criar o procedimento armazenado. O exemplo de SDK do .NET a seguir mostra um exemplo. Esta política tem suporte apenas na API básica (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Procedimento armazenado de resolução de conflito personalizado de exemplo

Procedimentos armazenados de resolução de conflitos personalizados devem ser implementados usando a assinatura de função mostrada abaixo. O nome da função não precisa corresponder ao nome usado ao registrar o procedimento armazenado com o contêiner, mas simplifica o nomear. Aqui está uma descrição dos parâmetros que devem ser implementados para esse procedimento armazenado.

- **incomingItem**: O item que está sendo inserido ou atualizado na confirmação que está gerando os conflitos. É nulo para operações de exclusão.
- **existingItem**: O item atualmente confirmado. Esse valor não é nulo em uma atualização e nulo para uma inserção ou exclusões.
- **isTombstone**: Booliano que indica se o incomingItem está em conflito com um item excluído anteriormente. Quando true, existingItem também é nulo.
- **conflictingItems**: Matriz da versão confirmada de todos os itens no contêiner que estão em conflito com incomingItem na ID ou qualquer outra propriedade de índice exclusivo.

> [!IMPORTANT]
> Assim como ocorre com qualquer procedimento armazenado, um procedimento de resolução de conflitos personalizado pode acessar quaisquer dados com a mesma chave de partição e pode executar qualquer operação de inserção, atualização ou exclusão para resolver conflitos.

Este procedimento armazenado de exemplo resolve conflitos selecionando o menor valor do `/myCustomId` caminho.

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>SDK DO .NET V2

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>SDK DO .NET V3

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>SDK do Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Depois que o contêiner for criado, você deverá criar `resolver` o procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>SDK do Java Sync

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Depois que o contêiner for criado, você deverá criar `resolver` o procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>SDK do Node.js/JavaScript/TypeScript

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

Depois que o contêiner for criado, você deverá criar `resolver` o procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>SDK do Python

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

Depois que o contêiner for criado, você deverá criar `resolver` o procedimento armazenado.

## <a name="create-a-custom-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos personalizada

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada. Esses conflitos aparecem no feed de conflitos.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>SDK DO .NET V2

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

### <a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>SDK DO .NET V3

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

### <a id="create-custom-conflict-resolution-policy-java-async"></a>SDK do Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>SDK do Java Sync

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>SDK do Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-python"></a>SDK do Python

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

Estes exemplos mostram como ler a partir do feed de conflitos de um contentor. Os conflitos aparecerão no feed de conflitos somente se não forem resolvidos automaticamente ou se você estiver usando uma política de conflito personalizada.

### <a id="read-from-conflict-feed-dotnet"></a>SDK DO .NET V2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-dotnet-v3"></a>SDK DO .NET V3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictIterator();
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

### <a id="read-from-conflict-feed-java-async"></a>SDK do Java Async

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>SDK do Java Sync

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>SDK do Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os seguintes conceitos de Azure Cosmos DB:

- [Distribuição global-nos bastidores](global-dist-under-the-hood.md)
- [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
- [Configurar clientes para hospedagem múltipla](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Adicionar ou remover regiões de sua conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md).
- [Criação de partições e distribuição de dados](partition-data.md)
- [Indexação no Azure Cosmos DB](indexing-policies.md)
