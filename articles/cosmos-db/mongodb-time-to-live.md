---
title: Funcionalidades TTL de cada documento do MongoDB no Azure Cosmos DB
description: Saiba como definir o tempo para o valor de TTL para documentos com a API do Azure Cosmos DB para o MongoDB automaticamente removê-los a partir do sistema após um período de tempo.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: cd6cb68014eea00077328f39c2c9bf0a1f7fb679
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436294"
---
# <a name="expire-data-with-azure-cosmos-dbs-api-for-mongodb"></a>Expirar dados com a API do Azure Cosmos DB para o MongoDB

A funcionalidade Time-to-live (TTL) permite à base de dados expirar automaticamente os dados. API do Azure Cosmos DB para o MongoDB utiliza funções de valor de TTL essenciais do Cosmos DB. São suportados dois modos: definir um valor TTL predefinido em toda a coleção e definir valores TTL individuais para cada documento. A lógica que regem a índices TTL e TTL por documento valores na API do Cosmos DB do MongoDB é o [mesmo do Cosmos DB](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>Índices TTL
Para ativar o TTL de forma universal numa coleção, tem de criar um ["Índice TTL" (índice time-to-live)](../cosmos-db/mongodb-indexing.md). O índice TTL é um índice no campo _ts com um valor "expireAfterSeconds".

Exemplo:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

O comando no exemplo acima irá criar um índice com a funcionalidade TTL. Depois de criar o índice, a base de dados irá eliminar automaticamente todos os documentos nessa coleção que não tenham sido modificados nos últimos 10 segundos. 

> [!NOTE]
> **_ts** é um campo específico do Cosmos DB e não está acessível a partir dos clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.
>
    
Além disso, um exemplo C#: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Definir o valor de TTL para um documento 
Os valores TTL por documento também são suportados. O(s) documento(s) tem(êm) de conter uma propriedade de nível de raiz "ttl" (minúscula) e deve ter criado um índice TTL, conforme descrito acima, para essa coleção. Os valores TTL definidos num documento irão substituir o valor de TTL da coleção.

O valor TTL tem de ser um número int32. Em alternativa, um int64 que se adeque a um int32 ou um valor duplo sem casa decimal que se adeque a um int32. São permitidos valores para a propriedade do TTL que não estão em conformidade com estas especificações, mas não são tratados como um valor TTL de documento significativo.

O valor TTL para o documento é opcional; os documentos sem um valor TTL podem ser inseridos na coleção.  Neste caso, será utilizado o valor de TTL da coleção. 

Os seguintes documentos têm valores TTL válidos. Assim que os documentos são introduzidos, os valores TTL do documento substituem valores de TTL da coleção. Por isso, os documentos serão removidos após 20 segundos.   

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

Os seguintes documentos têm valores TTL inválidos. Os documentos serão inseridos, mas o valor TTL do documento não será cumprido. Por isso, os documentos serão removidos após 10 segundos devido ao valor TTL da coleção. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>Como ativar a funcionalidade TTL por documento

A funcionalidade de valor de TTL por documento pode ser ativada com a API do Azure Cosmos DB para o MongoDB.

![Captura de ecrã da ativação do recurso TTL do documento por no Portal](./media/mongodb-ttl/mongodb_portal_ttl.png) 

## <a name="next-steps"></a>Passos Seguintes
* [Expirar dados no Azure Cosmos DB automaticamente com o tempo de duração](../cosmos-db/time-to-live.md)
* [Indexação da base de dados do Cosmos configurado com a API do Azure Cosmos DB para o MongoDB](../cosmos-db/mongodb-indexing.md)
