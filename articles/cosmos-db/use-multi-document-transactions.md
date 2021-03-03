---
title: Utilize transações multi-documentos na Azure Cosmos DB API para a MongoDB
description: Saiba como criar uma aplicação de concha mongo que pode executar uma transação multi-documento (tudo ou nada semântica) numa coleção fixa em Azure Cosmos DB API para MongoDB 4.0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692475"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Utilizar transações multi-documentos em Azure Cosmos DB API para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Neste artigo, irá criar uma aplicação de concha da Mongo que executa uma transação multi-documento numa coleção fixa em Azure Cosmos DB API para a MongoDB com a versão 4.0 do servidor.

## <a name="what-are-multi-document-transactions"></a>O que são transações multi-documentos?

Em Azure Cosmos DB API para MongoDB, as operações num único documento são atómicas. As transações multi-documentos permitem que as aplicações executem operações atómicas em vários documentos. Oferece semântica "tudo ou nada" às operações. Ao cometer, as alterações efecção efetivo dentro das transações são persistiu e se a transação falhar, todas as alterações dentro da transação são descartadas.

As transações multi-documentos seguem a semântica **acid:**

* Atomicidade: Todas as operações tratadas como uma só
* Consistência: Os dados comprometidos são válidos
* Isolamento: Isolado de outras operações
* Durabilidade: Os dados de transação persistem quando o cliente é informado assim

## <a name="requirements"></a>Requisitos

As transações multi-documentos são suportadas numa coleção nãohardificada na versão API 4.0. As transações multi-documentos não são suportadas através de coleções ou em coleções de fragmentos em 4.0. O tempo limite para transações é de 5 segundos fixos.

Todos os controladores que suportam a versão 4.0 ou superior do protocolo de fio suportarão a Azure Cosmos DB API para transações multi-documentos mongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Executar transações de vários documentos na concha de MongoDB
> [!Note]
> Este exemplo não funciona na beta (concha) da MongoSH incorporada na Bússola MongoDB.

1. Abra um pedido de comando, vá ao diretório onde a versão 4.0 da mongo shell e superior:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Crie um *script* deconnect_friends.jsde concha de mongo e adicione o seguinte conteúdo

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Execute o seguinte comando para executar a transação multi-documento. O hospedeiro, porta, utilizador e chave podem ser encontrados no portal Azure.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Passos seguintes

Explore as novidades em [Azure Cosmos DB API para MongoDB 4.0](mongodb-feature-support-40.md)
