---
title: Indexação na API do Azure Cosmos DB para MongoDB
description: Apresenta uma visão geral das capacidades de indexação com a API da Azure Cosmos DB para o MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029474"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação utilizando a API do Azure Cosmos DB para o MongoDB

A API da Azure Cosmos DB para a MongoDB alavanca as capacidades automáticas de gestão de índices da Cosmos DB. Como resultado, os utilizadores têm acesso às políticas de indexação padrão da Cosmos DB. Assim, se nenhum índice tiver sido definido pelo utilizador, ou se nenhum índice tiver sido baixado, então todos os campos serão automaticamente indexados por padrão quando inseridos numa coleção. Para a maioria dos cenários, recomendamos que utilize a política de indexação predefinida na conta.

## <a name="indexing-for-version-36"></a>Indexação para a versão 3.6

As contas que servem a versão 3.6 do protocolo de arame fornecem uma política de indexação padrão diferente da política fornecida por versões anteriores. Por padrão, apenas o campo _id está indexado. Para indexar campos adicionais, o utilizador deve aplicar os comandos de gestão de índices MongoDB. Para aplicar uma espécie a uma consulta, atualmente deve ser criado um índice nos campos utilizados na operação de tipo.

### <a name="dropping-the-default-indexes-36"></a>Baixa dos índices de incumprimento (3.6)

Para as contas que servem a versão 3.6 do protocolo de arame, o único índice predefinido é _id, que não pode ser baixado.

### <a name="creating-a-compound-index-36"></a>Criação de um índice composto (3.6)

Os verdadeiros índices compostos são suportados para contas utilizando o protocolo de 3.6 fios. O seguinte comando criará um índice composto nos campos 'a' e 'b': `db.coll.createIndex({a:1,b:1})`

Os índices compostos podem ser usados para classificar eficientemente em vários campos ao mesmo tempo, tais como: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Acompanhe o progresso do índice

A versão 3.6 da API da Azure Cosmos DB para contas MongoDB suporta o comando `currentOp()` para acompanhar o progresso do índice numa instância de base de dados. Este comando devolve um documento que contém informações sobre as operações em curso numa instância de base de dados. O comando `currentOp` é usado para acompanhar todas as operações em curso no MongoDB nativo, enquanto na API da Azure Cosmos DB para mongoDB, este comando apenas suporta o acompanhamento da operação do índice.

Aqui estão alguns exemplos que mostram como usar o comando `currentOp` para acompanhar o progresso do índice:

• Obtenha o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Obtenha o progresso do índice para todas as coleções numa base de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Obtenha o progresso do índice para todas as bases de dados e coleções numa conta Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Os detalhes de progresso do índice contêm percentagem de progresso para a operação do índice atual. O exemplo seguinte mostra o formato de documento de saída para diferentes fases do progresso do índice:

1. Se o funcionamento do índice numa base de dados de recolha 'foo' e de 'bar' que tenha uma indexação de 60 % completa, terá o seguinte documento de saída. `Inprog[0].progress.total` mostra 100 como o alvo a concluir.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Para uma operação de índice que acaba de começar numa base de dados de recolha 'foo' e 'bar', o documento de saída pode apresentar um progresso de 0% até atingir um nível mensurável.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Quando a operação do índice em curso estiver concluída, o documento de saída mostra operações inprog vazias.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indexação para a versão 3.2

### <a name="dropping-the-default-indexes-32"></a>Baixa dos índices de incumprimento (3.2)

O comando seguinte pode ser utilizado para remover os índices predefinidos para uma coleção ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Criação de um índice composto (3.2)

Os índices compostos contêm referências a vários campos de um documento. Logicamente, são equivalentes à criação de vários índices individuais por campo. Para tirar partido das otimizações fornecidas pelas técnicas de indexação do Cosmos DB, recomendamos que crie vários índices individuais em vez de um único índice composto (não exclusivo).

## <a name="common-indexing-operations"></a>Operações comuns de indexação

As seguintes operações são comuns para ambas as contas que servem a versão 3.6 do protocolo de arame e as contas que servem versões anteriores do protocolo de arame. 

## <a name="creating-unique-indexes"></a>Criar índices exclusivos

Os [índices exclusivos](unique-keys.md) são úteis para impor que não existem mais de dois documentos com o mesmo valor para os campos indexados.

>[!Important]
> Atualmente, os índices exclusivos podem ser criados apenas quando a coleção estiver vazia (sem documentos).

O seguinte comando cria um índice único no campo "student_id":

```shell
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

```shell
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

## <a name="migrating-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, apenas é possível criar índices exclusivos quando a coleção não tiver documentos. As ferramentas de migração populares do MongoDB tentam criar os índices exclusivos após a importação dos dados. Para contornar este problema, sugere-se que os utilizadores criem manualmente as coleções correspondentes e índices únicos, em vez de permitirem que a ferramenta de migração (para ```mongorestore``` este comportamento seja alcançado utilizando a bandeira `--noIndexRestore` na linha de comando).

## <a name="next-steps"></a>Passos seguintes

* [Indexação em Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados em Azure Cosmos DB automaticamente com tempo para viver](../cosmos-db/time-to-live.md)
