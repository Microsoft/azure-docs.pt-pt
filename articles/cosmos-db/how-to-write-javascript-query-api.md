---
title: Como escrever procedimentos armazenados e acionadores com a API de consulta JavaScript no Azure Cosmos DB
description: Saiba como escrever procedimentos armazenados e acionadores com a API de consulta JavaScript no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: f465ac91936b766d2c19ea8efd67b3acc8df6d75
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243935"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Como escrever procedimentos armazenados e acionadores no Azure Cosmos DB utilizando a API de consulta do JavaScript

O Azure Cosmos DB permite-lhe executar consultas otimizadas utilizando uma interface Fluente do JavaScript sem qualquer conhecimento da linguagem SQL que pode ser usada para gravar procedimentos armazenados ou disparadores. Para saber mais sobre o suporte de API de consulta do JavaScript no Azure Cosmos DB, veja [trabalhar com a linguagem JavaScript integrada a API de consulta no Azure Cosmos DB](javascript-query-api.md) artigo.

## <a id="stored-procedures"></a>Com a API de consulta de JavaScript do procedimento armazenado

O exemplo de código a seguir é um exemplo de como a consulta de JavaScript API é utilizada no contexto de um procedimento armazenado. O procedimento armazenado insere um item do Azure Cosmos DB, que é especificado por um parâmetro de entrada e um documento de metadados de atualizações utilizando o `__.filter()` método, com minSize, maxSize e totalSize com base na propriedade size do item de entrada.

> [!NOTE]
> `__` (valor de duplo sublinhado) é um alias para `getContext().getCollection()` ao utilizar a API de consulta do JavaScript.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre os procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB:

* [Como trabalhar com procedimentos armazenados, disparadores, funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como registar e utilizar os procedimentos armazenados no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Como registar e utilizar [pré-acionadores](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-acionadores](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) no Azure Cosmos DB

* [Como registar e utilizar as funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
