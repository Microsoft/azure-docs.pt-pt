---
title: Saiba como gerir conflitos entre regiões no Azure Cosmos DB
description: Saiba como gerir conflitos no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/16/2019
ms.author: mjbrown
ms.openlocfilehash: fb9850548f0bfb71b797830eb0d5fdfddbc32306
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054803"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gerir políticas de resolução de conflito no Azure Cosmos DB

Com gravações de várias regiões, se vários clientes escreverem no mesmo item, podem ocorrer conflitos. Quando ocorre um conflito, pode resolver o conflito ao utilizar políticas de resolução de conflito diferentes. Este artigo descreve como gerir políticas de resolução de conflito.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Criar uma política de resolução de conflito de última-escritor-wins

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflito de última-escritor-wins. O caminho padrão para o último-escritor-wins é o campo timestamp ou o `_ts` propriedade. Também pode ser definido para um caminho definido pelo utilizador para um tipo numérico. Num conflito, o valor mais alto wins. Se o caminho não está definido ou é inválido, é assumida como predefinição para `_ts`. Está em conflito resolvido com esta política não aparecem no feed de conflito. Esta política pode ser utilizada por todas as APIs.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>SDK do .NET

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
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Criar uma política de resolução de conflito personalizada utilizando um procedimento armazenado

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada com um procedimento armazenado para resolver o conflito. Esses conflitos não aparecem em conflito, exceto se houver um erro no seu procedimento armazenado do feed. Depois da política é criada com o contentor que tem de criar o procedimento armazenado. O exemplo de .NET SDK abaixo mostra um exemplo disso. Esta política é suportada no Core (SQL) Api apenas.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Resolução de conflitos personalizado de exemplo do procedimento armazenado

Procedimentos armazenado de resolução de conflito personalizado tem de ser implementados com a assinatura de função mostrada abaixo. O nome da função não tem de corresponder ao nome utilizado ao registar o procedimento armazenado com o contentor mas simplificar a atribuição de nomes. Eis uma descrição dos parâmetros que devem ser implementados para este procedimento armazenado.

- **incomingItem**: O item a ser inseridas ou atualizadas na consolidação que está a gerar a entra em conflito. É nulo para operações de eliminação.
- **existingItem**: O item atualmente confirmado. Este valor é nulo para uma inserção ou eliminação e de não-null numa atualização.
- **isTombstone**: Valor booleano que indica se o incomingItem está em conflito com um item eliminado anteriormente. Quando verdadeiro, existingItem também é nulo.
- **conflictingItems**: Matriz da versão consolidada de todos os itens no contentor que estão em conflito com incomingItem no id ou outras propriedades de índice exclusivo.

> [!IMPORTANT]
> Assim como acontece com qualquer procedimento armazenado, um procedimento de resolução de conflito personalizado pode aceder aos dados com a mesma chave de partição e pode efetuar qualquer inserir, atualizar ou eliminar operação para resolver conflitos.


Este exemplo de procedimento armazenado para resolver conflitos, selecionando o valor mais baixo do `/myCustomId` caminho.

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>SDK do .NET

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>SDK do Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Depois de criar o contentor, tem de criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>SDK do Java Sync

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Depois de criar o contentor, tem de criar o `resolver` procedimento armazenado.

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

Depois de criar o contentor, tem de criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>SDK do Python

```python
udp_collection = {
  'id': self.udp_collection_name,
  'conflictResolutionPolicy': {
      'mode': 'Custom',
      'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
      }
  }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

Depois de criar o contentor, tem de criar o `resolver` procedimento armazenado.


## <a name="create-a-custom-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos personalizada

Estes exemplos mostram como configurar um contentor com uma política de resolução de conflitos personalizada. Esses conflitos de aparecem no feed de conflito.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>SDK do .NET

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

Estes exemplos mostram como ler a partir do feed de conflitos de um contentor. Conflitos aparecem em conflito feed apenas se eles não foram resolvidos automaticamente ou se utilizar uma política personalizada do conflito.

### <a id="read-from-conflict-feed-dotnet"></a>SDK do .NET

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
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

Saiba mais sobre os seguintes conceitos do Azure Cosmos DB:

* [Distribuição global - sob definições avançadas](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar os clientes de multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Adicionar ou remover regiões a partir da sua conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md).
* [Criação de partições e distribuição de dados](partition-data.md)
* [Indexação no Azure Cosmos DB](indexing-policies.md)