---
title: Como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript no Azure Cosmos DB
description: Saiba como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 96ecd77cb955d5e63cdcae8657e3096bbbadba89
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092886"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Como escrever procedimentos armazenados e gatilhos em Azure Cosmos DB usando a API de consulta JavaScript

Azure Cosmos DB permite que você execute consultas otimizadas usando uma interface JavaScript fluente sem qualquer conhecimento da linguagem SQL que possa ser usada para gravar procedimentos armazenados ou gatilhos. Para saber mais sobre o suporte à API de consulta JavaScript no Azure Cosmos DB, consulte [trabalhando com API de consulta integrada à linguagem JavaScript no artigo Azure Cosmos DB](javascript-query-api.md) .

## <a id="stored-procedures"></a>Procedimento armazenado usando a API de consulta JavaScript

O exemplo de código a seguir é um exemplo de como a API de consulta JavaScript é usada no contexto de um procedimento armazenado. O procedimento armazenado insere um item Cosmos do Azure que é especificado por um parâmetro de entrada e atualiza um documento de metadados usando `__.filter()` o método, com minSize, MaxSize e totalSize com base na propriedade Size do item de entrada.

> [!NOTE]
> `__`(sublinhado duplo) é um alias para `getContext().getCollection()` quando usar a API de consulta JavaScript.

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

Consulte os artigos a seguir para saber mais sobre procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

* [Como trabalhar com procedimentos armazenados, gatilhos, funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como registrar e usar procedimentos armazenados no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Como registrar e usar [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) em Azure Cosmos DB

* [Como registrar e usar funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
