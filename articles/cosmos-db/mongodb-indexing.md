---
title: Indexação na API do Azure Cosmos DB para MongoDB
description: Apresenta uma visão geral das capacidades de indexação com a API da Azure Cosmos DB para o MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371064"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação utilizando a API do Azure Cosmos DB para o MongoDB

A API da Azure Cosmos DB para a MongoDB aproveita as capacidades de gestão de índices fundamentais da Azure Cosmos DB. Este documento centra-se em como adicionar índices usando a API do Azure Cosmos DB para o MongoDB. Você também pode ler uma [visão geral da indexação em Azure Cosmos DB](index-overview.md) que é relevante em todos os API's.

## <a name="indexing-for-version-36"></a>Indexação para a versão 3.6

O `_id` campo é sempre automaticamente indexado e não pode ser derrubado. A API da Azure Cosmos DB para mongoDB `_id` aplica automaticamente a singularidade do campo por chave de fragmentos.

Para indexar campos adicionais, deve aplicar os comandos de gestão de índices MongoDB. Tal como em MongoDB, apenas o `_id` campo é indexado automaticamente. Esta política de indexação padrão é diferente da API Azure Cosmos DB SQL, que indexa todos os campos por padrão.

Para aplicar uma espécie a uma consulta, deve ser criado um índice nos campos utilizados na operação de tipo.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Pode criar índices em qualquer campo. A ordem de tipo do índice de campo único não importa. O seguinte comando criará um `name`índice no campo:

`db.coll.createIndex({name:1})`

Uma consulta utilizará vários índices de campo únicos, sempre que disponível.

### <a name="compound-indexes-36"></a>Índices compostos (3.6)

Os índices compostos são suportados para contas utilizando o protocolo de 3.6 fios. Pode incluir até 8 campos num índice composto. Ao contrário do MongoDB, você só deve criar um índice composto se a sua consulta precisar de classificar eficientemente em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam de classificar, deve criar múltiplos índices de campo único em vez de um único índice composto.

O seguinte comando criará um `name` índice `age`composto nos campos e:

`db.coll.createIndex({name:1,age:1})`

Os índices compostos podem ser usados para classificar eficientemente em vários campos ao mesmo tempo, tais como:

`db.coll.find().sort({name:1,age:1})`

O índice composto acima também pode ser usado para uma triagem eficiente em uma consulta com a ordem de classificação oposta em todos os campos. Segue-se um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a sequência dos caminhos no índice composto deve corresponder exatamente à consulta. Aqui está um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Índices multichave

A Azure Cosmos DB cria índices multichave para indexar o conteúdo armazenado em matrizes. Se indexar um campo com um valor de matriz, o Azure Cosmos DB indexa automaticamente todos os elementos da matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais beneficiarão de índices geoespaciais. Atualmente, a API da Azure Cosmos DB `2dsphere` para mongoDB suporta índices. `2d`índices ainda não são suportados.

Aqui está um exemplo para criar um `location` índice geoespacial no campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

Os índices de texto não são atualmente suportados. Para consultas de pesquisa de texto em cordas, você deve usar a integração [da Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) com o Azure Cosmos DB.

## <a name="index-properties"></a>Propriedades indexadas

As seguintes operações são comuns para ambas as contas que servem a versão 3.6 do protocolo de arame e as contas que servem versões anteriores do protocolo de arame. Também pode aprender mais sobre [índices suportados e propriedades indexadas.](mongodb-feature-support-36.md#indexes-and-index-properties)

### <a name="unique-indexes"></a>Índices exclusivos

Os [índices exclusivos](unique-keys.md) são úteis para impor que não existem mais de dois documentos com o mesmo valor para os campos indexados.

>[!Important]
> Os índices únicos só podem ser criados quando a coleção está vazia (não contém documentos).

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

Para coleções esfumaçadas, a criação de um índice único requer fornecer a chave de fragmentos (partição). Por outras palavras, todos os índices exclusivos numa coleção em partição horizontal são índices compostos em que um dos campos é a chave de partição.

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

### <a name="ttl-indexes"></a>Índices TTL

Para ativar a expiração de documentos numa coleção específica, é necessário criar um ["Índice TTL" (índice time-to-live)](../cosmos-db/time-to-live.md). Um índice TTL é um índice no campo _ts com um valor "expireAfterSeconds".

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior irá causar a eliminação de todos os documentos na coleção ```db.coll``` que não foram modificados nos últimos 10 segundos.

> [!NOTE]
> **_ts** é um campo específico da Azure Cosmos e não é acessível a clientes da MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.

## <a name="track-the-index-progress"></a>Acompanhe o progresso do índice

A versão 3.6 da API da Azure Cosmos DB para `currentOp()` contas MongoDB suporta o comando para acompanhar o progresso do índice numa instância de base de dados. Este comando devolve um documento que contém informações sobre as operações em curso numa instância de base de dados. O `currentOp` comando é usado para rastrear todas as operações em curso no MongoDB nativo, enquanto na API da Azure Cosmos DB para mongoDB, este comando apenas suporta o acompanhamento da operação do índice.

Aqui estão alguns exemplos `currentOp` que mostram como usar o comando para acompanhar o progresso do índice:

* Obtenha o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obtenha o progresso do índice para todas as coleções numa base de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenha o progresso do índice para todas as bases de dados e coleções numa conta Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Os detalhes de progresso do índice contêm percentagem de progresso para a operação do índice atual. O exemplo seguinte mostra o formato de documento de saída para diferentes fases do progresso do índice:

1. Se o funcionamento do índice numa base de dados de recolha 'foo' e de 'bar' que tenha uma indexação de 60 % completa, terá o seguinte documento de saída. `Inprog[0].progress.total`mostra 100 como a conclusão do alvo.

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

### <a name="background-index-updates"></a>Atualizações do índice de fundo

Independentemente do valor especificado para a propriedade do índice **background,** as atualizações de índice são sempre feitas em segundo plano. As atualizações de índices consomem RU's com uma prioridade menor do que outras operações de base de dados. Portanto, as alterações de índice não resultarão em qualquer tempo de inatividade para escritos, atualizações ou exclusões.

Ao adicionar um novo índice, as consultas irão utilizá-lo imediatamente. Isto significa que as consultas podem não devolver todos os resultados correspondentes, e fá-lo-ão sem responder a quaisquer erros. Uma vez concluída a transformação do índice, os resultados da consulta serão consistentes. Pode [acompanhar o progresso do índice.](#track-the-index-progress)

## <a name="migrating-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, apenas é possível criar índices exclusivos quando a coleção não tiver documentos. As ferramentas de migração populares do MongoDB tentam criar os índices exclusivos após a importação dos dados. Para contornar este problema, sugere-se que os utilizadores criem manualmente as coleções correspondentes ```mongorestore``` e índices únicos, `--noIndexRestore` em vez de permitirem que a ferramenta de migração (para este comportamento é alcançado utilizando a bandeira na linha de comando).

## <a name="indexing-for-version-32"></a>Indexação para a versão 3.2

Para as contas DoB SBB compatíveis com a versão 3.2 do protocolo de arame MongoDB, as funcionalidades de indexação disponíveis e os incumprimentos são diferentes. Pode [verificar a versão da sua conta.](mongodb-feature-support-36.md#protocol-support) Pode fazer upgrade para a versão 3.6 apresentando um pedido de [suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se estiver a utilizar a versão 3.2, esta secção descreve diferenças-chave com a versão 3.6.

### <a name="dropping-the-default-indexes-32"></a>Baixa dos índices de incumprimento (3.2)

Ao contrário da versão 3.6 da API do Azure Cosmos DB para mongoDB, a versão 3.2 indexa todas as propriedades por padrão. O seguinte comando pode ser utilizado para deixar ```coll```cair estes índices predefinidos para uma recolha:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de baixar os índices predefinidos, pode adicionar índices adicionais como feito na versão 3.6.

### <a name="compound-indexes-32"></a>Índices compostos (3.2)

Os índices compostos contêm referências a vários campos de um documento. Se quiser criar um índice composto, por favor atualize para versão 3.6, apresentando um pedido de [suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados em Azure Cosmos DB automaticamente com tempo para viver](../cosmos-db/time-to-live.md)
