---
title: Tutorial para configurar a distribuição global com a API de Azure Cosmos DB para MongoDB
description: Saiba como configurar a distribuição global usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: b446697977395aa9bbbcf2192aa232fbc85a0b68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444674"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Configurar o banco de dados distribuído global usando a API do Azure Cosmos DB para MongoDB

Neste artigo, mostramos como usar o portal do Azure para configurar um banco de dados distribuído global e conectar-se a ele usando a API do Azure Cosmos DB para MongoDB.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global usando a [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Verificando a configuração regional 
Uma maneira simples de verificar sua configuração global com a API de Cosmos DB para MongoDB é executar o comando *IsMaster ()* do Shell Mongo.

A partir da Shell de Mongo:

   ```
      db.isMaster()
   ```
   
Resultados de exemplo:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Conectando-se a uma região preferencial 

A API do Azure Cosmos DB para MongoDB permite que você especifique a preferência de leitura da sua coleção para um banco de dados distribuído globalmente. Para leituras de baixa latência e elevada disponibilidade global, recomendamos a definição da preferência de leitura da coleção como *mais próxima*. Está configurada uma preferência de leitura de *mais próxima* para ler a partir da região mais próxima.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Para aplicações com uma região de leitura/escrita primária e uma região secundária para cenários de recuperação após desastre (DR), recomendamos a definição da preferência de leitura da coleção como *secundária preferida*. Está configurada uma preferência de leitura de *secundária preferida* para ler a partir da região secundária quando a região primária não estiver disponível.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Por último, se quiser especificar manualmente as regiões de leitura. Pode definir a região Etiqueta na sua preferência de leitura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Já está, isto conclui este tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente ao ler [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). Para obter mais informações sobre como funciona a replicação de base de dados global no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global usando a API do Cosmos DB para MongoDB

Agora, pode avançar para o próximo tutorial para saber como desenvolver localmente com o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador de Azure Cosmos DB](local-emulator.md)
