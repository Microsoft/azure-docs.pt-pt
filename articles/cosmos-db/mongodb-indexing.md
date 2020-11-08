---
title: Gerir a indexação na API da Azure Cosmos para a MongoDB
description: Este artigo apresenta uma visão geral das capacidades de indexação da Azure Cosmos DB utilizando a API da Azure Cosmos DB para a MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 11/06/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: e920af85c511387e66bcafcb6a140844d25f204c
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369295"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gerir a indexação na API da Azure Cosmos para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A API da Azure Cosmos DB para a MongoDB tira partido das capacidades de gestão de índices fundamentais da Azure Cosmos DB. Este artigo centra-se em como adicionar índices usando a API da Azure Cosmos DB para a MongoDB. Também pode ler uma [visão geral da indexação em Azure Cosmos DB](index-overview.md) que é relevante em todas as APIs.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexação para a versão 3.6 do servidor MongoDB

A API do Azure Cosmos DB para a versão 3.6 do servidor MongoDB indexa automaticamente o `_id` campo, que não pode ser eliminado. Impõe automaticamente a singularidade do `_id` campo por chave de fragmentos. Na API da Azure Cosmos DB para o MongoDB, o sharding e a indexação são conceitos separados. Não tens de indexar a tua chave de fragmentos. No entanto, como em qualquer outra propriedade no seu documento, se esta propriedade é um filtro comum nas suas consultas, recomendamos indexar a chave de fragmentos.

Para indexar campos adicionais, aplique os comandos de gestão de índices do MongoDB. Tal como em MongoDB, a API da Azure Cosmos para a MongoDB indexa automaticamente o `_id` campo apenas. Esta política de indexação predefinida difere da API SQL do Azure Cosmos DB, que indexa todos os campos por predefinição.

Para aplicar uma espécie a uma consulta, é necessário criar um índice nos campos utilizados na operação de classificação.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Pode criar índices em qualquer campo. A ordem do índice de campo único não importa. O seguinte comando cria um índice no `name` campo:

`db.coll.createIndex({name:1})`

Uma consulta utiliza vários índices de campo único sempre que disponível. Pode criar até 500 índices de campo únicos por contentor.

### <a name="compound-indexes-mongodb-server-version-36"></a>Índices compostos (versão 3.6 do servidor MongoDB)

A API da Azure Cosmos DB para o MongoDB suporta índices compostos para contas que utilizam o protocolo de fio da versão 3.6. Pode incluir até oito campos num índice composto. Ao contrário do MongoDB, você deve criar um índice composto apenas se a sua consulta precisar de classificar eficientemente em vários campos ao mesmo tempo. Para consultas com filtros múltiplos que não precisam de classificar, crie vários índices de campo único em vez de um único índice composto. 

> [!NOTE]
> Não é possível criar índices compostos em propriedades aninhadas ou matrizes.

O seguinte comando cria um índice composto nos campos `name` `age` e:

`db.coll.createIndex({name:1,age:1})`

Pode utilizar índices compostos para classificar eficientemente em vários campos ao mesmo tempo, como mostra o exemplo seguinte:

`db.coll.find().sort({name:1,age:1})`

Também pode usar o índice composto anterior para classificar eficientemente numa consulta com a ordem de classificação oposta em todos os campos. Eis um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a sequência dos caminhos no índice composto deve corresponder exatamente à consulta. Aqui está um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> Os índices compostos são usados apenas em consultas que classificam resultados. Para consultas que tenham vários filtros que não precisam de classificar, crie índices de campo único multipe.

### <a name="multikey-indexes"></a>Índices multi-chave

A Azure Cosmos DB cria índices multi-chave para indexar conteúdo armazenado em matrizes. Se indexar um campo com um valor de matriz, a Azure Cosmos DB indexa automaticamente todos os elementos da matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais beneficiarão de índices geoespaciais. Atualmente, a API da Azure Cosmos DB para o MongoDB suporta `2dsphere` índices. A API ainda não suporta `2d` índices.

Aqui está um exemplo de criação de um índice geoespacial no `location` campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

A API da Azure Cosmos DB para o MongoDB não suporta atualmente índices de texto. Para consultas de pesquisa de texto em cordas, você deve usar a integração [de Pesquisa Cognitiva Azure](../search/search-howto-index-cosmosdb.md) com Azure Cosmos DB. 

## <a name="wildcard-indexes"></a>Índices wildcard

Você pode usar índices wildcard para suportar consultas contra campos desconhecidos. Imaginemos que tem uma coleção que contém dados sobre famílias.

Aqui está parte de um documento de exemplo nessa coleção:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Aqui está outro exemplo, desta vez com um conjunto ligeiramente diferente de propriedades `children` em:

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

Nesta coleção, os documentos podem ter muitas propriedades possíveis diferentes. Se quiser indexar todos os dados da `children` matriz, tem duas opções: criar índices separados para cada propriedade individual ou criar um índice wildcard para toda a `children` matriz.

### <a name="create-a-wildcard-index"></a>Criar um índice wildcard

O seguinte comando cria um índice wildcard em quaisquer propriedades dentro `children` de :

`db.coll.createIndex({"children.$**" : 1})`

**Ao contrário do que existe em MongoDB, os índices wildcard podem suportar vários campos em predicados de consulta.** Não haverá uma diferença no desempenho da consulta se utilizar um único índice wildcard em vez de criar um índice separado para cada propriedade.

Pode criar os seguintes tipos de índice utilizando a sintaxe wildcard:

- Campo único
- Geoespacial

### <a name="indexing-all-properties"></a>Indexação de todas as propriedades

Eis como se pode criar um índice wildcard em todos os campos:

`db.coll.createIndex( { "$**" : 1 } )`

> [!NOTE]
> Se está apenas a começar o desenvolvimento, recomendamos **vivamente** começar com um índice wildcard em todos os campos. Isto pode simplificar o desenvolvimento e facilitar a otimização de consultas.

Os documentos com muitos campos podem ter uma elevada taxa de Unidade de Pedido (RU) para escritas e atualizações. Portanto, se tiver uma carga de trabalho pesada, deve optar por percursos de índice individualmente em oposição à utilização de índices wildcard.

### <a name="limitations"></a>Limitações

Os índices wildcard não suportam nenhum dos seguintes tipos ou propriedades de índices:

- Composto
- TTL
- Exclusivo

**Ao contrário do que acontece em MongoDB,** na API da Azure Cosmos DB para a MongoDB **não pode** utilizar índices wildcard para:

- Criar um índice wildcard que inclui vários campos específicos

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Criar um índice wildcard que exclui vários campos específicos

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

Como alternativa, pode criar vários índices wildcard.

## <a name="index-properties"></a>Propriedades de índice

As seguintes operações são comuns para contas que servem a versão 3.6 do protocolo de fio e contas que servem versões anteriores. Pode aprender mais sobre [índices suportados e propriedades indexadas.](mongodb-feature-support-36.md#indexes-and-index-properties)

### <a name="unique-indexes"></a>Índices exclusivos

[Índices únicos](unique-keys.md) são úteis para impor que dois ou mais documentos não contêm o mesmo valor para campos indexados.

> [!IMPORTANT]
> Índices únicos só podem ser criados quando a coleção estiver vazia (não contém documentos).

O seguinte comando cria um índice único no `student_id` campo:

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

Para coleções de fragmentos, deve fornecer a chave fragmento (partição) para criar um índice único. Por outras palavras, todos os índices exclusivos numa coleção em partição horizontal são índices compostos em que um dos campos é a chave de partição.

Os seguintes comandos criam uma coleção de ```coll``` fragmentos (a chave de fragmento ```university``` é) com um índice único nos campos `student_id` `university` e:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
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

Para permitir a expiração do documento numa determinada coleção, é necessário criar um [índice time-to-live (TTL).](../cosmos-db/time-to-live.md) Um índice TTL é um índice no `_ts` campo com um `expireAfterSeconds` valor.

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior elimina quaisquer documentos da ```db.coll``` coleção que não tenham sido modificados nos últimos 10 segundos.

> [!NOTE]
> O campo **_ts** é específico da Azure Cosmos DB e não está acessível a clientes mongoDB. É uma propriedade reservada (sistema) que contém o carimbo de tempo da última modificação do documento.

## <a name="track-index-progress"></a>Acompanhar o progresso do índice

A versão 3.6 da API da Azure Cosmos DB para a MongoDB suporta o comando para acompanhar o progresso do `currentOp()` índice numa instância de base de dados. Este comando devolve um documento que contém informações sobre operações em curso numa instância de base de dados. Usas o `currentOp` comando para rastrear todas as operações em progresso na MongoDB nativa. Na API da Azure Cosmos DB para a MongoDB, este comando só suporta o rastreio da operação de índice.

Aqui estão alguns exemplos que mostram como usar o comando para acompanhar o `currentOp` progresso do índice:

* Obtenha o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obtenha o progresso do índice para todas as coleções numa base de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenha o progresso do índice para todas as bases de dados e coleções numa conta da Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Exemplos de produção de progresso do índice

Os detalhes do progresso do índice mostram a percentagem de progresso para a operação do índice atual. Aqui está um exemplo que mostra o formato do documento de saída para diferentes fases do progresso do índice:

- Uma operação de índice numa base de dados "foo" e "bar" que esteja 60% completa terá o seguinte documento de saída. O `Inprog[0].progress.total` campo mostra 100 como a percentagem de conclusão do alvo.

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

- Se uma operação de índice tiver começado numa base de dados de recolha "foo" e "bar", o documento de saída pode mostrar um progresso de 0% até atingir um nível mensurável.

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

- Quando a operação do índice em curso termina, o documento de saída mostra `inprog` operações vazias.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>Atualizações de índice de fundo

Independentemente do valor especificado para a propriedade do índice **de fundo,** as atualizações de índice são sempre feitas em segundo plano. Uma vez que as atualizações de índice consomem Unidades de Pedido (RUs) com uma prioridade inferior à de outras operações de base de dados, as alterações de índice não resultarão em tempo de inatividade para as escritas, atualizações ou eliminações.

Não há impacto em ler disponibilidade ao adicionar um novo índice. As consultas só utilizarão novos índices uma vez que a transformação do índice esteja completa. Durante a transformação do índice, o motor de consulta continuará a utilizar os índices existentes, pelo que observará um desempenho de leitura semelhante durante a transformação de indexação ao que tinha observado antes de iniciar a alteração de indexação. Ao adicionar novos índices, também não existe o risco de resultados de consulta incompletos ou inconsistentes.

Ao remover índices e fazer imediatamente consultas, os filtros nos índices caídos, os resultados podem ser inconsistentes e incompletos até que a transformação do índice termine. Se remover índices, o motor de consulta não fornece resultados consistentes ou completos quando as consultas filtram estes índices recentemente removidos. A maioria dos desenvolvedores não baixa índices e, em seguida, tenta imediatamente consultá-los para que, na prática, esta situação seja improvável.

> [!NOTE]
> Pode [acompanhar o progresso do índice.](#track-index-progress)

## <a name="reindex-command"></a>Comando ReIndex

O `reIndex` comando recriará todos os índices de uma coleção. Na maioria dos casos, isto é desnecessário. No entanto, em alguns casos raros, o desempenho da consulta pode melhorar após a execução do `reIndex` comando.

Pode executar o `reIndex` comando utilizando a seguinte sintaxe:

`db.runCommand({ reIndex: <collection> })`

Pode utilizar a sintaxe abaixo para verificar se precisa de executar o `reIndex` comando:

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

Resultado do exemplo:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

Se `reIndex` for necessário, **requer QuereIndex** será verdade. Se `reIndex` não for necessário, esta propriedade será omitida.

## <a name="migrate-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, só é possível criar índices únicos quando a coleção não contém documentos. As ferramentas de migração populares do MongoDB tentam criar os índices únicos depois de importar os dados. Para contornar este problema, pode criar manualmente as coleções correspondentes e índices únicos em vez de permitir que a ferramenta de migração experimente. (Pode alcançar este comportamento ```mongorestore``` utilizando a bandeira na linha de `--noIndexRestore` comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indexação para a versão 3.2 do MongoDB

As funcionalidades e incumprimentos de indexação disponíveis são diferentes para as contas Azure Cosmos que são compatíveis com a versão 3.2 do protocolo de fio MongoDB. Pode [consultar a versão da sua conta](mongodb-feature-support-36.md#protocol-support) e atualizar para a versão [3.6](mongodb-version-upgrade.md).

Se estiver a utilizar a versão 3.2, esta secção descreve as principais diferenças com a versão 3.6.

### <a name="dropping-default-indexes-version-32"></a>Queda de índices predefinidos (versão 3.2)

Ao contrário da versão 3.6 da API da Azure Cosmos DB para a MongoDB, a versão 3.2 indexa cada propriedade por padrão. Pode utilizar o seguinte comando para deixar cair estes índices predefinidos para uma cobrança ```coll``` (

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de baixar os índices predefinidos, pode adicionar mais índices como na versão 3.6.

### <a name="compound-indexes-version-32"></a>Índices compostos (versão 3.2)

Os índices compostos contêm referências a vários campos de um documento. Se quiser criar um índice composto, [atualize para a versão 3.6](mongodb-version-upgrade.md).

### <a name="wildcard-indexes-version-32"></a>Índices wildcard (versão 3.2)

Se quiser criar um índice wildcard, [atualize para a versão 3.6](mongodb-version-upgrade.md).

## <a name="next-steps"></a>Passos seguintes

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire os dados em Azure Cosmos DB automaticamente com tempo de vida](../cosmos-db/time-to-live.md)
* Para conhecer a relação entre a divisão e a indexação, veja como consultar um artigo de [contentores Azure Cosmos.](how-to-query-container.md)
