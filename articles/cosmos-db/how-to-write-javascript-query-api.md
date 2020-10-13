---
title: Escreva procedimentos e gatilhos armazenados utilizando a API de consulta JavaScript em Azure Cosmos DB
description: Saiba como escrever procedimentos e gatilhos armazenados utilizando a API de Consulta JavaScript em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 4251a9ec0766f6e1956e4885b58dc874e01b0960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253076"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Como escrever procedimentos e gatilhos armazenados em Azure Cosmos DB utilizando a consulta JavaScript API

O Azure Cosmos DB permite-lhe executar consultas otimizadas utilizando uma interface JavaScript fluente sem qualquer conhecimento da linguagem SQL que pode ser usada para escrever procedimentos ou gatilhos armazenados. Para saber mais sobre o suporte da API da Consulta JavaScript em Azure Cosmos DB, consulte [Trabalhar com a API integrada de linguagem JavaScript em artigo DB do Cosmos Azure.](javascript-query-api.md)

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Procedimento armazenado utilizando a API de consulta JavaScript

A amostra de código a seguir é um exemplo de como a consulta JavaScript API é usada no contexto de um procedimento armazenado. O procedimento armazenado insere um item Azure Cosmos que é especificado por um parâmetro de entrada e atualiza um documento de metadados utilizando o `__.filter()` método, com minSize, maxSize e totalSize com base na propriedade do tamanho do item de entrada.

> [!NOTE]
> `__` (duplo-sublinhado) é um pseudónimo para `getContext().getCollection()` quando se utiliza a API de consulta JavaScript.

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

Consulte os seguintes artigos para saber sobre procedimentos armazenados, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB:

* [Como trabalhar com procedimentos armazenados, gatilhos, funções definidas pelo utilizador em Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como registar e utilizar procedimentos armazenados na Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Como registar e usar [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) em Azure Cosmos DB

* [Como registar e utilizar funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
