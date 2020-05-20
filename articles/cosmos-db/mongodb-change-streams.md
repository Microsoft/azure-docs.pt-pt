---
title: Alterar streams na API da Azure Cosmos DB para o MongoDB
description: Saiba como utilizar os fluxos de mudança na API do Azure Cosmos DB para a MongoDB para obter as alterações feitas aos seus dados.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: cc6b74a56d2a538d35e324090832e6c7e03e609f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647301"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar streams na API da Azure Cosmos DB para o MongoDB

O suporte [para alimentação](change-feed.md) de alterações na API da Azure Cosmos DB para o MongoDB está disponível utilizando os fluxos de mudança API. Ao utilizar os fluxos de alteração API, as suas aplicações podem obter as alterações feitas na recolha ou nos itens num único fragmento. Mais tarde poderá tomar mais medidas com base nos resultados. As alterações aos itens da coleção são capturadas na ordem do seu tempo de modificação e a ordem de classificação é garantida por chave de fragmentos.

[!NOTE]
Para utilizar os streams de mudança, crie a conta com a versão 3.6 da API do Azure Cosmos DB para o MongoDB, ou uma versão posterior. Se executar os exemplos de fluxo de mudança contra uma versão anterior, poderá ver o `Unrecognized pipeline stage name: $changeStream` erro.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações aplicam-se quando se utilizam fluxos de mudança:

* As `operationType` propriedades e propriedades ainda não são suportadas no documento de `updateDescription` saída.
* Os `insert` `update` tipos de operações e `replace` operações são atualmente suportados. 
* A eliminação da operação ou de outros eventos ainda não são suportados.

Devido a estas limitações, as opções $match fase, $project palco e fullDocument são necessárias como mostrado nos exemplos anteriores.

Ao contrário do feed de mudança no SQL API da Azure Cosmos DB, não existe uma Biblioteca separada de [processadores de feed](change-feed-processor.md) de mudança para consumir fluxos de mudança ou a necessidade de um recipiente de arrendamento. Não existe atualmente suporte para [os gatilhos das Funções Azure](change-feed-functions.md) para processar fluxos de mudança.

## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados quando utilizam os fluxos de mudança:

* Código de **erro HTTP 16500** - Quando o fluxo de mudança é estrangulado, devolve uma página vazia.

* **NamespaceNotFound (OperationType Invalida)** - Se executar o fluxo de alteração na recolha que não existe ou se a recolha for abandonada, então um `NamespaceNotFound` erro é devolvido. Como a `operationType` propriedade não pode ser devolvida no documento de saída, em vez do `operationType Invalidate` erro, o erro é `NamespaceNotFound` devolvido.

## <a name="examples"></a>Exemplos

O exemplo que se segue mostra como obter fluxos de mudança em todos os itens da coleção. Este exemplo cria um cursor para observar itens quando são inseridos, atualizados ou substituídos. O `$match` palco, `$project` o palco e `fullDocument` a opção são necessários para obter os fluxos de mudança. Atualmente, não é suportado o cuidado de eliminar as operações utilizando fluxos de mudança. Como uma suver, pode adicionar um marcador suave nos itens que estão a ser eliminados. Por exemplo, pode adicionar um atributo no item chamado "eliminado". Quando quiser apagar o item, pode definir "apagado" `true` e definir um TTL no item. Uma vez que atualizar "eliminado" `true` para uma atualização, esta alteração será visível no fluxo de alterações.

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

## <a name="changes-within-a-single-shard"></a>Alterações dentro de um único fragmento

O exemplo seguinte mostra como obter alterações nos itens dentro de um único fragmento. Este exemplo obtém as alterações de itens que têm chave de fragmento igual a "a" e o valor-chave do fragmento igual a "1". É possível ter diferentes clientes a ler mudanças de diferentes fragmentos em paralelo.

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

As seguintes limitações aplicam-se quando se utilizam fluxos de mudança:

* As `operationType` propriedades e propriedades ainda não são suportadas no documento de `updateDescription` saída.
* Os `insert` `update` tipos de operações e `replace` operações são atualmente suportados. A eliminação da operação ou de outros eventos ainda não são suportados.

Devido a estas limitações, as opções $match fase, $project palco e fullDocument são necessárias como mostrado nos exemplos anteriores.

## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados quando utilizam os fluxos de mudança:

* Código de **erro HTTP 429** - Quando o fluxo de mudança é estrangulado, devolve uma página vazia.

* **NamespaceNotFound (OperationType Invalida)** - Se executar o fluxo de alteração na recolha que não existe ou se a recolha for abandonada, então um `NamespaceNotFound` erro é devolvido. Como a `operationType` propriedade não pode ser devolvida no documento de saída, em vez do `operationType Invalidate` erro, o erro é `NamespaceNotFound` devolvido.

## <a name="next-steps"></a>Passos seguintes

* [Use o tempo para viver para expirar dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)