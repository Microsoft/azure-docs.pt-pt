---
title: Alterar streams na API da Azure Cosmos DB para o MongoDB
description: Saiba como utilizar os fluxos de mudança na API do Azure Cosmos DB para a MongoDB para obter as alterações feitas aos seus dados.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467782"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar streams na API da Azure Cosmos DB para o MongoDB

O suporte [para alimentação](change-feed.md) de alterações na API da Azure Cosmos DB para o MongoDB está disponível utilizando os fluxos de mudança API. Ao utilizar os fluxos de alteração API, as suas aplicações podem obter as alterações feitas na recolha ou nos itens num único fragmento. Mais tarde poderá tomar mais medidas com base nos resultados. As alterações aos itens da coleção são capturadas na ordem do seu tempo de modificação e a ordem de classificação é garantida por chave de fragmentos.

> [!NOTE]
> Para utilizar os streams de mudança, crie a conta com a versão 3.6 da API do Azure Cosmos DB para o MongoDB, ou uma versão posterior. Se executar os exemplos de fluxo de mudança contra `Unrecognized pipeline stage name: $changeStream` uma versão anterior, poderá ver o erro. 

O exemplo que se segue mostra como obter fluxos de mudança em todos os itens da coleção. Este exemplo cria um cursor para observar itens quando são inseridos, atualizados ou substituídos. A $match fase, $project estágio e a opção fullDocument são necessárias para obter os fluxos de mudança. Atualmente, não é suportado o cuidado de eliminar as operações utilizando fluxos de mudança. Como uma suver, pode adicionar um marcador suave nos itens que estão a ser eliminados. Por exemplo, pode adicionar um atributo no item chamado "eliminado" e defini-lo como "verdadeiro" e definir um TTL no item, para que possa eliminá-lo automaticamente, bem como rastreá-lo.

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

O exemplo que se segue mostra como obter alterações nos itens num único fragmento. Este exemplo obtém as alterações de itens que têm chave de fragmento igual a "a" e o valor-chave do fragmento igual a "1".

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

* As `operationType` `updateDescription` propriedades e propriedades ainda não são suportadas no documento de saída.
* Os `insert` `update`tipos `replace` de operações e operações são atualmente suportados. A eliminação da operação ou de outros eventos ainda não são suportados.

Devido a estas limitações, as opções $match fase, $project palco e fullDocument são necessárias como mostrado nos exemplos anteriores.

## <a name="error-handling"></a>Processamento de erros

Os seguintes códigos de erro e mensagens são suportados quando utilizam os fluxos de mudança:

* Código de **erro HTTP 429** - Quando o fluxo de mudança é estrangulado, devolve uma página vazia.

* **NamespaceNotFound (OperationType Invalida)** - Se executar o fluxo de alteração na recolha que `NamespaceNotFound` não existe ou se a recolha for abandonada, então um erro é devolvido. Como `operationType` a propriedade não pode ser devolvida no `operationType Invalidate` documento de `NamespaceNotFound` saída, em vez do erro, o erro é devolvido.

## <a name="next-steps"></a>Passos seguintes

* [Use o tempo para viver para expirar dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
