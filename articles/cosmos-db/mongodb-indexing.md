---
title: Gerir a indexação na API da Azure Cosmos DB para o MongoDB
description: Este artigo apresenta uma visão geral das capacidades de indexação do Azure Cosmos DB utilizando a API MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732711"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gerir a indexação na API da Azure Cosmos DB para o MongoDB

A API da Azure Cosmos DB para a MongoDB aproveita as principais capacidades de gestão de índices da Azure Cosmos DB. Este artigo centra-se em como adicionar índices usando a API do Azure Cosmos DB para o MongoDB. Você também pode ler uma [visão geral da indexação em Azure Cosmos DB](index-overview.md) que é relevante em todas as APIs.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexação para a versão 3.6 do servidor MongoDB

A API do Azure Cosmos DB para a versão 3.6 `_id` do servidor MongoDB indexa automaticamente o campo, que não pode ser largado. Aplica automaticamente a singularidade do `_id` campo por chave de fragmentos.

Para indexar campos adicionais, aplica os comandos de gestão de índices MongoDB. Tal como em MongoDB, a API da Azure Cosmos DB `_id` para o MongoDB indexa automaticamente o campo apenas. Esta política de indexação padrão difere da API Azure Cosmos DB SQL, que indexa todos os campos por padrão.

Para aplicar uma espécie a uma consulta, deve criar um índice sobre os campos utilizados na operação de tipo.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Pode criar índices em qualquer campo. A ordem de tipo do índice de campo único não importa. O seguinte comando cria um `name`índice no campo:

`db.coll.createIndex({name:1})`

Uma consulta utiliza vários índices de campo únicos, sempre que disponível. Pode criar até 500 índices de campo únicos por recipiente.

### <a name="compound-indexes-mongodb-server-version-36"></a>Índices compostos (versão 3.6 do servidor MongoDB)

A API da Azure Cosmos DB para o MongoDB suporta índices compostos para contas que utilizam o protocolo de arame versão 3.6. Pode incluir até oito campos num índice composto. Ao contrário do MongoDB, você deve criar um índice composto apenas se a sua consulta precisar de classificar eficientemente em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam de classificar, crie múltiplos índices de campo únicos em vez de um único índice composto.

O seguinte comando cria um `name` índice `age`composto nos campos e:

`db.coll.createIndex({name:1,age:1})`

Pode utilizar índices compostos para classificar eficientemente em vários campos ao mesmo tempo, como mostra o seguinte exemplo:

`db.coll.find().sort({name:1,age:1})`

Também pode usar o índice composto anterior para classificar eficientemente uma consulta com a ordem de classificação oposta em todos os campos. Segue-se um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a sequência dos caminhos no índice composto deve corresponder exatamente à consulta. Aqui está um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Índices multichave

A Azure Cosmos DB cria índices multichave para indexar o conteúdo armazenado em matrizes. Se indexar um campo com um valor de matriz, o Azure Cosmos DB indexa automaticamente todos os elementos da matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais beneficiarão de índices geoespaciais. Atualmente, a API da Azure Cosmos DB `2dsphere` para mongoDB suporta índices. A API ainda `2d` não suporta índices.

Aqui está um exemplo de criação `location` de um índice geoespacial no campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

A API da Azure Cosmos DB para o MongoDB não suporta atualmente índices de texto. Para consultas de pesquisa de texto em cordas, você deve usar a integração [de Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) com O Azure Cosmos DB.

## <a name="index-properties"></a>Propriedades indexadas

As seguintes operações são comuns para contas que servem a versão 3.6 do protocolo de arame e as contas que servem versões anteriores. Você pode aprender mais sobre [índices suportados e propriedades indexadas](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Índices exclusivos

[Os índices únicos](unique-keys.md) são úteis para impor que dois ou mais documentos não contêm o mesmo valor para campos indexados.

> [!IMPORTANT]
> Os índices únicos só podem ser criados quando a coleção está vazia (não contém documentos).

O seguinte comando cria um `student_id`índice único no campo:

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

Para coleções esfumaçadas, deve fornecer a chave de fragmentos (partição) para criar um índice único. Por outras palavras, todos os índices exclusivos numa coleção em partição horizontal são índices compostos em que um dos campos é a chave de partição.

Os seguintes comandos criam ```coll``` uma coleção esfardita (a chave do fragmento é) ```university```com um índice único nos campos `student_id` e: `university`

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

No exemplo anterior, omitir a ```"university":1``` cláusula devolve um erro com a seguinte mensagem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Índices TTL

Para permitir a expiração do documento numa determinada coleção, é necessário criar um índice de [tempo para viver (TTL).](../cosmos-db/time-to-live.md) Um índice TTL é `_ts` um índice `expireAfterSeconds` no campo com um valor.

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior elimina quaisquer ```db.coll``` documentos da coleção que não tenham sido modificados nos últimos 10 segundos.

> [!NOTE]
> O campo **_ts** é específico do Azure Cosmos DB e não é acessível a clientes da MongoDB. É uma propriedade reservada (sistema) que contém o carimbo temporal da última modificação do documento.

## <a name="track-index-progress"></a>Acompanhar o progresso do índice

A versão 3.6 da API da Azure Cosmos DB `currentOp()` para o MongoDB suporta o comando para acompanhar o progresso do índice numa instância de base de dados. Este comando devolve um documento que contém informações sobre operações em curso numa instância de base de dados. Usas `currentOp` o comando para rastrear todas as operações em andamento na mongoDB nativa. Na API da Azure Cosmos DB para o MongoDB, este comando apenas suporta o acompanhamento da operação do índice.

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

### <a name="examples-of-index-progress-output"></a>Exemplos de produção de progresso do índice

Os detalhes do progresso do índice mostram a percentagem de progresso para a operação do índice atual. Aqui está um exemplo que mostra o formato de documento de saída para diferentes fases do progresso do índice:

- Uma operação de índice numa base de dados de recolha "foo" e "bar" que esteja 60% completa terá o seguinte documento de saída. O `Inprog[0].progress.total` campo mostra 100 como a percentagem de conclusão do alvo.

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

- Se uma operação de índice acaba de começar numa base de dados de recolha "foo" e "bar", o documento de saída pode mostrar um progresso de 0% até atingir um nível mensurável.

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

- Quando a operação do índice em curso termina, o documento de saída mostra operações vazias. `inprog`

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Atualizações do índice de fundo

Independentemente do valor especificado para a propriedade do índice **background,** as atualizações de índice são sempre feitas em segundo plano. Uma vez que as atualizações de índices consomem Unidades de Pedido (RUs) com uma prioridade menor do que outras operações de base de dados, as alterações de índice não resultarão em qualquer tempo de inatividade para escritos, atualizações ou exclusões.

Quando adicionar um novo índice, as consultas usarão imediatamente o índice. Isto significa que as consultas podem não devolver todos os resultados correspondentes e fá-lo-ão sem responder a quaisquer erros. Quando a transformação do índice estiver concluída, os resultados da consulta serão consistentes. Pode acompanhar o [progresso do índice.](#track-index-progress)

## <a name="migrate-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, só é possível criar índices únicos quando a coleção não contém documentos. As ferramentas populares de migração do MongoDB tentam criar os índices únicos após importar os dados. Para contornar este problema, pode criar manualmente as coleções correspondentes e índices únicos em vez de permitir que a ferramenta de migração tente. (Pode alcançar este ```mongorestore``` comportamento através `--noIndexRestore` da utilização da bandeira na linha de comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indexação para a versão 3.2 do MongoDB

As funcionalidades de indexação disponíveis e os incumprimentos são diferentes para as contas da Azure Cosmos que são compatíveis com a versão 3.2 do protocolo de arame MongoDB. Pode [verificar a versão da sua conta.](mongodb-feature-support-36.md#protocol-support) Pode fazer upgrade para a versão 3.6 apresentando um pedido de [suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se estiver a utilizar a versão 3.2, esta secção descreve as principais diferenças com a versão 3.6.

### <a name="dropping-default-indexes-version-32"></a>Queda dos índices predefinidos (versão 3.2)

Ao contrário da versão 3.6 da API do Azure Cosmos DB para o MongoDB, a versão 3.2 indexa todas as propriedades por padrão. Pode utilizar o seguinte comando para deixar cair```coll```estes índices predefinidos para uma recolha ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de baixar os índices predefinidos, pode adicionar mais índices como faria na versão 3.6.

### <a name="compound-indexes-version-32"></a>Índices compostos (versão 3.2)

Os índices compostos contêm referências a vários campos de um documento. Se quiser criar um índice composto, atualize para a versão 3.6 apresentando um pedido de [suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados em Azure Cosmos DB automaticamente com tempo para viver](../cosmos-db/time-to-live.md)
