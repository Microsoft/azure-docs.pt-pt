---
title: Tutorial de distribuição global com a API do Azure Cosmos DB do MongoDB
description: Saiba como configurar a distribuição global com a API do Azure Cosmos DB do MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5ae5923253575fc3dea6b90b599b9fa3d79a85b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578818"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Configurar global base de dados distribuída, com a API do Azure Cosmos DB do MongoDB

Neste artigo, vamos mostrar como utilizar o portal do Azure para configurar uma base de dados de distribuição global e ligá-la com a API do Azure Cosmos DB do MongoDB.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com o [API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Verificar a configuração regional 
Uma forma simple de verificar a configuração global com a API do Cosmos DB do MongoDB é executar o *ismaster ()* comando a partir da Shell de Mongo.

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

## <a name="connecting-to-a-preferred-region"></a>Ligar a uma região preferencial 

API do Azure Cosmos DB do MongoDB permite-lhe especificar a preferência de leitura de sua coleção para uma base de dados distribuído globalmente. Para leituras de baixa latência e elevada disponibilidade global, recomendamos a definição da preferência de leitura da coleção como *mais próxima*. Está configurada uma preferência de leitura de *mais próxima* para ler a partir da região mais próxima.

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
> * Configurar a distribuição global com a API do Cosmos DB do MongoDB

Agora, pode avançar para o próximo tutorial para saber como desenvolver localmente com o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador do Azure Cosmos DB](local-emulator.md)
