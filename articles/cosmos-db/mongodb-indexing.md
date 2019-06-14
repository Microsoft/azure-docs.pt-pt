---
title: Indexar na API do Azure Cosmos DB para o MongoDB
description: Apresenta uma visão geral das capacidades de indexação com a API do Azure Cosmos DB para o MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: de037316efa50dd25ea04c370fa0e5878fb52ba1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448508"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação com a API do Azure Cosmos DB do MongoDB

API do Azure Cosmos DB para o MongoDB tira partido das capacidades de gestão de índices automática do Cosmos DB. Como resultado, os utilizadores têm acesso às políticas do Cosmos DB de indexação predefinidas. Então, se não tiver definido pelo utilizador, ou tem sido removidos sem índices, em seguida, todos os campos serão automaticamente indexados por predefinição quando inserido numa coleção. Para a maioria dos cenários, recomendamos que utilize a política de indexação predefinida na conta.

## <a name="dropping-the-default-indexes"></a>Remover os índices predefinidos

O comando seguinte pode ser utilizado para remover os índices predefinidos para uma coleção ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Criar índices compostos

Os índices compostos contêm referências a vários campos de um documento. Logicamente, são equivalentes à criação de vários índices individuais por campo. Para tirar partido das otimizações fornecidas pelas técnicas de indexação do Cosmos DB, recomendamos que crie vários índices individuais em vez de um único índice composto (não exclusivo).

## <a name="creating-unique-indexes"></a>Criar índices exclusivos

Os [índices exclusivos](unique-keys.md) são úteis para impor que não existem mais de dois documentos com o mesmo valor para os campos indexados. 
>[!important] 
> Atualmente, os índices exclusivos podem ser criados apenas quando a coleção estiver vazia (sem documentos). 

O comando seguinte cria um índice exclusivo no campo "student_id":

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Para coleções em partição horizontal, de acordo com o comportamento do MongoDB, criar um índice exclusivo necessita de uma chave de partição horizontal (partição). Por outras palavras, todos os índices exclusivos numa coleção em partição horizontal são índices compostos em que um dos campos é a chave de partição.

Os comandos seguintes criam uma coleção em partição horizontal ```coll``` (a chave de partição horizontal é ```university```) com um índice exclusivo nos campos student_id e university:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

No exemplo acima, se a cláusula ```"university":1``` for omitida, é devolvido um erro com a seguinte mensagem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Índices TTL

Para ativar a expiração de documentos numa coleção específica, é necessário criar um ["Índice TTL" (índice time-to-live)](../cosmos-db/time-to-live.md). Um índice TTL é um índice no campo _ts com um valor "expireAfterSeconds".
 
Exemplo:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior irá causar a eliminação de todos os documentos na coleção ```db.coll``` que não foram modificados nos últimos 10 segundos. 
 
> [!NOTE]
> **_ts** é um campo específico do Cosmos DB e não está acessível a partir dos clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.
>

## <a name="migrating-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, apenas é possível criar índices exclusivos quando a coleção não tiver documentos. As ferramentas de migração populares do MongoDB tentam criar os índices exclusivos após a importação dos dados. Para contornar este problema, é sugerido que os utilizadores criem manualmente as coleções correspondentes e os índices exclusivos, em vez de permitir a ferramenta de migração (para ```mongorestore```, este comportamento é conseguido com o sinalizador --noIndexRestore na linha de comandos).

## <a name="next-steps"></a>Passos Seguintes
* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados no Azure Cosmos DB automaticamente com o tempo de duração](../cosmos-db/time-to-live.md)
