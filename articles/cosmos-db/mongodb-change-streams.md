---
title: Alterar fluxos na API da Azure Cosmos DB para MongoDB
description: Saiba como utilizar a API da Azure Cosmos DB para a MongoDB para obter as alterações feitas nos seus dados.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9a4e35a235fe6ee6950bd1b4c35cbf9e72ac2893
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359239"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar fluxos na API da Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Alterar](change-feed.md) o suporte de feed na API da Azure Cosmos DB para o MongoDB está disponível utilizando os fluxos de alteração API. Ao utilizar os fluxos de alteração API, as suas aplicações podem obter as alterações feitas na recolha ou nos itens num único fragmento. Mais tarde poderá tomar outras ações com base nos resultados. As alterações aos itens da coleção são capturadas na ordem do seu tempo de modificação e a ordem de classificação é garantida por chave de fragmentos.

> [!NOTE]
> Para utilizar os streams de alteração, crie a conta com a versão 3.6 da API da Azure Cosmos DB para o MongoDB, ou uma versão posterior. Se executar os exemplos de alteração de stream contra uma versão anterior, poderá ver o `Unrecognized pipeline stage name: $changeStream` erro.

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter fluxos de mudança em todos os itens da coleção. Este exemplo cria um cursor para observar os itens quando são inseridos, atualizados ou substituídos. A `$match` fase, `$project` o palco e `fullDocument` a opção são necessários para obter os fluxos de mudança. Atualmente, não é suportado o uso de operações de eliminação através de fluxos de alteração. Como solução alternativa, pode adicionar um marcador suave nos itens que estão a ser eliminados. Por exemplo, pode adicionar um atributo no item chamado "eliminado". Quando pretender eliminar o item, pode definir "apagado" `true` e definir um TTL no item. Uma vez que a atualização "eliminada" `true` é uma atualização, esta alteração será visível no fluxo de alterações.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra como utilizar a funcionalidade de fluxo de mudança em Java, para o exemplo completo, consulte este [repo GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). Este exemplo também mostra como usar o `resumeAfter` método para procurar todas as alterações da última leitura. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Alterações dentro de um único fragmento

O exemplo a seguir mostra como obter alterações nos itens dentro de um único fragmento. Este exemplo obtém as alterações de itens que têm chave de fragmento igual a "a" e o valor chave de fragmento igual a "1". É possível ter diferentes clientes a ler mudanças de diferentes fragmentos em paralelo.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis quando se utilizam fluxos de alteração:

* As `operationType` propriedades e propriedades ainda não `updateDescription` estão suportadas no documento de saída.
* Os `insert` `update` tipos de `replace` operações estão atualmente suportados. No entanto, a operação de eliminação ou outros eventos ainda não estão suportados.

Devido a estas limitações, a fase $match, $project fase e opções fullDocument são necessárias como mostrado nos exemplos anteriores.

Ao contrário do feed de mudança no API SQL da Azure Cosmos DB, não existe uma Biblioteca separada do [processador change feed](change-feed-processor.md) para consumir fluxos de mudança ou a necessidade de um recipiente de arrendamento. Atualmente, não existe suporte para [gatilhos de Funções Azure](change-feed-functions.md) para processar fluxos de mudança.

## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados quando utilizam streams de alteração:

* **Código de erro HTTP 16500** - Quando o fluxo de alteração é acelerado, retorna uma página vazia.

* **NamespaceNotFound (OperationType Invalidado)** - Se executar o fluxo de alteração na coleção que não existe ou se a coleção for largada, então `NamespaceNotFound` um erro é devolvido. Como a `operationType` propriedade não pode ser devolvida no documento de saída, em vez do `operationType Invalidate` erro, o erro é `NamespaceNotFound` devolvido.

## <a name="next-steps"></a>Passos seguintes

* [Use o tempo para viver para expirar automaticamente na API da Azure Cosmos DB para a MongoDB](mongodb-time-to-live.md)
* [Indexação na API da Azure Cosmos DB para a MongoDB](mongodb-indexing.md)
