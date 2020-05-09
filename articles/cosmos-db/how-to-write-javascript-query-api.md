---
title: Escreva procedimentos e gatilhos armazenados usando a API de consulta JavaScript em Azure Cosmos DB
description: Saiba escrever procedimentos e gatilhos armazenados usando a API javaScript consulta em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.openlocfilehash: bc92a1276232506d291e367f74a9dcf8d58d7e07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982331"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Como escrever procedimentos e gatilhos armazenados em Azure Cosmos DB utilizando a API de consulta JavaScript

O Azure Cosmos DB permite-lhe realizar consultas otimizadas utilizando uma interface fluente javaScript sem qualquer conhecimento da linguagem SQL que possa ser usada para escrever procedimentos ou gatilhos armazenados. Para saber mais sobre o suporte da API javaScript consulta em Azure Cosmos DB, consulte Trabalhar com a API integrada em linguagem JavaScript no artigo [Da Azure Cosmos DB.](javascript-query-api.md)

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Procedimento armazenado utilizando a API de consulta JavaScript

A amostra de código que se segue é um exemplo de como a API de consulta JavaScript é usada no contexto de um procedimento armazenado. O procedimento armazenado insere um item Azure Cosmos especificado por um parâmetro de entrada, `__.filter()` e atualiza um documento de metadados utilizando o método, com minSize, maxSize e totalSize com base na propriedade de tamanho do item de entrada.

> [!NOTE]
> `__`(sublinhado duplo) é um `getContext().getCollection()` pseudónimo para quando se utiliza a API de consulta JavaScript.

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

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para conhecer procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB:

* [Como trabalhar com procedimentos armazenados, gatilhos, funções definidas pelo utilizador em Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como registar e utilizar procedimentos armazenados em Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Como registar e utilizar [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) em Azure Cosmos DB

* [Como registar e utilizar funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
