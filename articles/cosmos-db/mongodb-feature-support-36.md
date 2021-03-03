---
title: A API da Azure Cosmos DB para a versão MongoDB (versão 3.6) suportada e sintaxe
description: Saiba mais sobre a API suportada pela Azure Cosmos DB para a versão MongoDB (versão 3.6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce79b450e5eaed04150ffafd88528a131417044a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692322"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>API do Azure Cosmos DB para MongoDB (versão 3.6): sintaxe e funcionalidades suportadas
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode comunicar com a API da Azure Cosmos DB para a MongoDB usando qualquer um dos [motoristas](https://docs.mongodb.org/ecosystem/drivers)de clientes de código aberto MongoDB . A API do Azure Cosmos DB para MongoDB permite a utilização dos controladores cliente existentes através do [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Ao utilizar a API da Azure Cosmos para a MongoDB, poderá usufruir dos benefícios do MongoDB a que está habituado, com todas as capacidades empresariais que a Cosmos DB fornece: [distribuição global, fragmentos](distribute-data-globally.md) [automáticos,](partitioning-overview.md)disponibilidade e garantias de latência, encriptação em repouso, backups e muito mais.

> [!NOTE]
> A versão 3.6 da API cosmos DB para a MongoDB não tem planos atuais para o fim da vida (EOL). O aviso mínimo para um futuro EOL é de três anos.

## <a name="protocol-support"></a>Apoio ao Protocolo

A API do Azure Cosmos DB para MongoDB é compatível com a versão **3.6** do servidor MongoDB por predefinição para as novas contas. Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. Qualquer controlador do cliente que compreenda estes protocolos deverá conseguir ligar à API do Azure Cosmos DB para MongoDB. Note que ao utilizar a API da Azure Cosmos para contas MongoDB, a versão 3.6 da conta tem o ponto final no `*.mongo.cosmos.azure.com` formato, enquanto a versão 3.2 da conta tem o ponto final no formato `*.documents.azure.com` .

## <a name="query-language-support"></a>Suporte linguístico de consulta

A API da Azure Cosmos DB para o MongoDB fornece um suporte abrangente para construções linguísticas de consulta mongoDB. As secções seguintes mostram a lista detalhada de operações de servidores, operadores, estágios, comandos e opções atualmente suportadas pela Azure Cosmos DB.

> [!NOTE]
> Este artigo apenas lista os comandos do servidor suportado e exclui as funções de invólucro do lado do cliente. Funções de invólucro do lado do cliente, tais como `deleteMany()` e `updateMany()` utilizar internamente os `delete()` comandos e `update()` servidor. As funções que utilizam comandos de servidor suportados são compatíveis com a API da Azure Cosmos DB para o MongoDB.

## <a name="database-commands"></a>Comandos da base de dados

A API da Azure Cosmos DB para a MongoDB suporta os seguintes comandos de base de dados:

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita

| Comando | Suportado |
|---------|---------|
| [mudar fluxos](mongodb-change-streams.md) | Sim |
| delete | Sim |
| eval | Não |
| find | Sim |
| findAndModify | Sim |
| getLastError | Sim |
| getMore | Sim |
| obterPrevError | Não |
| insert | Sim |
| paraleloCollectionScan | Não |
| resetEror | Não |
| update | Sim |

### <a name="authentication-commands"></a>Comandos de autenticação

| Comando | Suportado |
|---------|---------|
| authenticate | Sim |
| getnonce | Sim |
| logout | Sim |

### <a name="administration-commands"></a>Comandos de administração

| Comando | Suportado |
|---------|---------|
| cloneCollectionAsCapped | Não |
| collMod | Não |
| connectionStatus | Não |
| converteToCapped | Não |
| copydb | Não |
| criar | Sim |
| createIndexes | Sim |
| atualOp | Sim |
| drop | Sim |
| dropDatabase | Sim |
| dropIndexes | Sim |
| filemd5 | Sim |
| killCursors | Sim |
| killOp | Não |
| listCollections | Sim |
| listDatabases | Sim |
| listIndexes | Sim |
| reIndex | Sim |
| renomeCollecto | Não |


### <a name="diagnostics-commands"></a>Comandos de diagnóstico

| Comando | Suportado |
|---------|---------|
| buildInfo | Sim |
| collStats | Sim |
| ConnPoolStats | Não |
| connectionStatus | Não |
| dataSize | Não |
| dbHash | Não |
| dbStats | Sim |
| explicar | Sim |
| funcionalidades | Não |
| hostInfo | Sim |
| listDatabases | Sim |
| listCommands | Não |
| perfis | Não |
| serverStatus | Não |
| top | Não |
| whatsmyuri | Sim |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

### <a name="aggregation-commands"></a>Comandos de agregação

| Comando | Suportado |
|---------|---------|
| aggregate | Sim |
| count | Sim |
| distinct | Sim |
| mapReduce | Não |

### <a name="aggregation-stages"></a>Fases de agregação

| Comando | Suportado |
|---------|---------|
| $addFields | Sim |
| $bucket | Não |
| $bucketAuto | Não |
| $changeStream | Sim |
| $collStats | Não |
| $count | Sim |
| $currentOp | Não |
| $facet | Sim |
| $geoNear | Sim |
| $graphLookup | Sim |
| $group | Sim |
| $indexStats | Não |
| $limit | Sim |
| $listLocalSessions | Não |
| $listSessions | Não |
| $lookup | Sim |
| $match | Sim |
| $out | Sim |
| $project | Sim |
| $redact | Sim |
| $replaceRoot | Sim |
| $replaceWith | Não |
| $sample | Sim |
| $skip | Sim |
| $sort | Sim |
| $sortByCount | Sim |
| $unwind | Sim |

### <a name="boolean-expressions"></a>Expressões booleanas

| Comando | Suportado |
|---------|---------|
| $and | Sim |
| $not | Sim |
| $or | Sim |

### <a name="set-expressions"></a>Expressões de definição

| Comando | Suportado |
|---------|---------|
| $setEquals | Sim |
| $setIntersection | Sim |
| $setUnion | Sim |
| $setDifference | Sim |
| $setIsSubset | Sim |
| $anyElementTrue | Sim |
| $allElementsTrue | Sim |

### <a name="comparison-expressions"></a>Expressões de comparação

| Comando | Suportado |
|---------|---------|
| $cmp | Sim |
| $eq | Sim | 
| $gt | Sim | 
| $gte | Sim | 
| $lt | Sim |
| $lte | Sim | 
| $ne | Sim | 
| $in | Sim | 
| $nin | Sim | 

### <a name="arithmetic-expressions"></a>Expressões aritméticas

| Comando | Suportado |
|---------|---------|
| $abs | Sim |
| $add | Sim |
| $ceil | Sim |
| $divide | Sim |
| $exp | Sim |
| $floor | Sim |
| $ln | Sim |
| $log | Sim |
| $log10 | Sim |
| $mod | Sim |
| $multiply | Sim |
| $pow | Sim |
| $sqrt | Sim |
| $subtract | Sim |
| $trunc | Sim |

### <a name="string-expressions"></a>Expressões de cadeia

| Comando | Suportado |
|---------|---------|
| $concat | Sim |
| $indexOfBytes | Sim |
| $indexOfCP | Sim |
| $split | Sim |
| $strLenBytes | Sim |
| $strLenCP | Sim |
| $strcasecmp | Sim |
| $substr | Sim |
| $substrBytes | Sim |
| $substrCP | Sim |
| $toLower | Sim |
| $toUpper | Sim |

### <a name="text-search-operator"></a>Operador de pesquisa de texto

| Comando | Suportado |
|---------|---------|
| $meta | Não |

### <a name="array-expressions"></a>Expressões de matriz

| Comando | Suportado |
|---------|---------|
| $arrayElemAt | Sim |
| $arrayToObject | Sim |
| $concatArrays | Sim |
| $filter | Sim |
| $indexOfArray | Sim |
| $isArray | Sim |
| $objectToArray | Sim |
| $range | Sim |
| $reverseArray | Sim |
| $reduce | Sim |
| $size | Sim |
| $slice | Sim |
| $zip | Sim |
| $in | Sim |

### <a name="variable-operators"></a>Operadores variáveis

| Comando | Suportado |
|---------|---------|
| $map | Sim |
| $let | Sim |

### <a name="system-variables"></a>Variáveis do sistema

| Comando | Suportado |
|---------|---------|
| $$CURRENT | Sim |
| $$DESCEND | Sim |
| $$KEEP | Sim |
| $$PRUNE | Sim |
| $$REMOVE | Sim |
| $$ROOT | Sim |

### <a name="literal-operator"></a>Operador literal

| Comando | Suportado |
|---------|---------|
| $literal | Sim |

### <a name="date-expressions"></a>Expressões de data

| Comando | Suportado |
|---------|---------|
| $dayOfYear | Sim |
| $dayOfMonth | Sim |
| $dayOfWeek | Sim |
| $year | Sim |
| $month | Sim | 
| $week | Sim |
| $hour | Sim |
| $minute | Sim | 
| $second | Sim |
| $millisecond | Sim | 
| $dateToString | Sim |
| $isoDayOfWeek | Sim |
| $isoWeek | Sim |
| $dateFromParts | Não | 
| $dateToParts | Não |
| $dateFromString | Não |
| $isoWeekYear | Sim |

### <a name="conditional-expressions"></a>Expressões condicionais

| Comando | Suportado |
|---------|---------|
| $cond | Sim |
| $ifNull | Sim |
| $switch | Sim |

### <a name="data-type-operator"></a>Operador de tipo de dados

| Comando | Suportado |
|---------|---------|
| $type | Sim |

### <a name="accumulator-expressions"></a>Expressões acumuladoras

| Comando | Suportado |
|---------|---------|
| $sum | Sim |
| $avg | Sim |
| $first | Sim |
| $last | Sim |
| $max | Sim |
| $min | Sim |
| $push | Sim |
| $addToSet | Sim |
| $stdDevPop | Sim |
| $stdDevSamp | Sim |

### <a name="merge-operator"></a>Operador de fusão

| Comando | Suportado |
|---------|---------|
| $mergeObjects | Sim |

## <a name="data-types"></a>Tipos de dados

| Comando | Suportado |
|---------|---------|
| Double (Duplo) | Sim |
| String | Sim |
| Objeto | Sim |
| Matriz | Sim |
| Dados Binários | Sim | 
| ObjectId | Sim |
| Booleano | Sim |
| Data | Sim |
| Nulo | Sim |
| Inteiro de 32 bits (int) | Sim |
| CarimboDeDataEHora | Sim |
| 64-bit Inteiro (longo) | Sim |
| MinKey | Sim |
| MaxKey | Sim |
| Decimal128 | Sim | 
| Expressão Regular | Sim |
| JavaScript | Sim |
| JavaScript (com âmbito)| Sim |
| Indefinido | Sim |

## <a name="indexes-and-index-properties"></a>Índices e propriedades de índice

### <a name="indexes"></a>Índices

| Comando | Suportado |
|---------|---------|
| Índice de Campo Único | Sim |
| Índice Composto | Sim |
| Índice Multikey | Sim |
| Índice de Texto | Não |
| 2desfera | Sim |
| Índice 2d | Não |
| Índice de Haxixe | Sim |

### <a name="index-properties"></a>Propriedades de índice

| Comando | Suportado |
|---------|---------|
| TTL | Sim |
| Exclusivo | Sim |
| Parcial | Não |
| Caso Insensível | Não |
| Disperso | Não |
| Fundo | Sim |

## <a name="operators"></a>Operadores

### <a name="logical-operators"></a>Operadores lógicos

| Comando | Suportado |
|---------|---------|
| $or | Sim |
| $and | Sim |
| $not | Sim |
| $nor | Sim | 

### <a name="element-operators"></a>Operadores de elementos

| Comando | Suportado |
|---------|---------|
| $exists | Sim |
| $type | Sim |

### <a name="evaluation-query-operators"></a>Operadores de consulta de avaliação

| Comando | Suportado |
|---------|---------|
| $expr | Não |
| $jsonSchema | Não |
| $mod | Sim |
| $regex | Sim |
| $text | Não (Não suportado. Use $regex em vez disso.)| 
| $where | Não | 

Nas consultas $regex, expressões ancoradas à esquerda permitem a procura de índices. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.

Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex. Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})``` - tem de ser modificada da seguinte forma:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```

A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas. O operador barra "|" atua como uma função "or" – a consulta ```find({x:{$regex: /^abc |^def/})``` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operadores de matriz

| Comando | Suportado | 
|---------|---------|
| $all | Sim | 
| $elemMatch | Sim | 
| $size | Sim | 

### <a name="comment-operator"></a>Operador de comentários

| Comando | Suportado | 
|---------|---------|
| $comment | Sim | 

### <a name="projection-operators"></a>Operadores de projeção

| Comando | Suportado |
|---------|---------|
| $elemMatch | Sim |
| $meta | Não |
| $slice | Sim |

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo

| Comando | Suportado |
|---------|---------|
| $inc | Sim |
| $mul | Sim |
| $rename | Sim |
| $setOnInsert | Sim |
| $set | Sim |
| $unset | Sim |
| $min | Sim |
| $max | Sim |
| $currentDate | Sim |

#### <a name="array-update-operators"></a>Operadores de atualização de matriz

| Comando | Suportado |
|---------|---------|
| $ | Sim |
| $[]| Sim |
| $[<identifier>]| Sim |
| $addToSet | Sim |
| $pop | Sim |
| $pullAll | Sim |
| $pull | Sim |
| $push | Sim |
| $pushAll | Sim |


#### <a name="update-modifiers"></a>Atualizar modificadores

| Comando | Suportado |
|---------|---------|
| $each | Sim |
| $slice | Sim |
| $sort | Sim |
| $position | Sim |

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit

| Comando | Suportado |
|---------|---------|
| $bit | Sim | 
| $bitsAllSet | Não |
| $bitsAnySet | Não |
| $bitsAllClear | Não |
| $bitsAnyClear | Não |

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Suportado | 
--- | --- |
$geoWithin | Sim |
$geoIntersects | Sim | 
$near | Sim |
$nearSphere | Sim |
$geometry | Sim |
$minDistance | Sim |
$maxDistance | Sim |
$center | Não |
$centerSphere | Não |
$box | Não |
$polygon | Não |

## <a name="sort-operations"></a>Ordenar operações

Ao utilizar a `findOneAndUpdate` operação, as operações de classificação num único campo são suportadas, mas as operações de classificação em vários campos não são suportadas.

## <a name="unique-indexes"></a>Índices exclusivos

[Índices únicos](mongodb-indexing.md#unique-indexes) garantem que um campo específico não tem valores duplicados em todos os documentos de uma coleção, semelhante à forma como a singularidade é preservada na chave "_id" padrão. Pode criar índices únicos em Cosmos DB utilizando o `createIndex` comando com o parâmetro de `unique` restrição:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Índices compostos

[Os índices compostos](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) fornecem uma forma de criar um índice para grupos de campos para até 8 campos. Este tipo de índice difere dos índices compostos mongoDB nativos. Em Azure Cosmos DB, os índices compostos são usados para operações de triagem que são aplicadas em vários campos. Para criar um índice composto é necessário especificar mais do que uma propriedade como parâmetro:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

A Azure Cosmos DB suporta o GridFS através de qualquer controlador MongoDB compatível com GridFS.

## <a name="replication"></a>Replicação

Cosmos DB suporta replicação automática e nativa nas camadas mais baixas. Esta lógica é expandida para conseguir também a replicação global de latência baixa. Cosmos DB não suporta comandos de replicação manual.





## <a name="retryable-writes"></a>Redações Retripáveis

AZure Cosmos DB ainda não suporta redações retripáveis. Os condutores de clientes devem adicionar `retryWrites=false` à sua cadeia de ligação.

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB suporta a fragmentação automática do lado do servidor. Gere a criação, colocação e equilíbrio de fragmentos automaticamente. A Azure Cosmos DB não suporta comandos manuais de sharding, o que significa que não tem de invocar comandos como addShard, balancerStart, moveChunk etc. Basta especificar a chave de fragmentos enquanto cria os recipientes ou consulta os dados.

## <a name="sessions"></a>Sessões

A Azure Cosmos DB ainda não suporta comandos de sessões do lado do servidor.

## <a name="time-to-live-ttl"></a>TTL

A Azure Cosmos DB suporta um time-to-live (TTL) baseado no tempotamp do documento. A TTL pode ser ativada para coleções a partir do [portal Azure.](https://portal.azure.com)

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

O Azure Cosmos DB ainda não suporta utilizadores e funções. No entanto, suporta o controlo de acesso baseado em funções Azure (Azure RBAC) e palavras-passe ou chaves de leitura e leitura que podem ser obtidas através do painel de cordas de ligação no [portal Azure](https://portal.azure.com).

## <a name="write-concern"></a>Preocupação Escrita

Algumas aplicações baseiam-se numa [Comissão de Escrita](https://docs.mongodb.com/manual/reference/write-concern/) que especifica o número de respostas necessárias durante uma operação de escrita. Devido à forma como a Azure Cosmos DB lida com a replicação, todas as escritas são automaticamente o quórum maioritário por padrão ao usar uma forte consistência. Qualquer preocupação de escrita especificada pelo código do cliente é ignorada. Para saber mais, consulte [Usando níveis de consistência para maximizar a disponibilidade e](consistency-levels.md) o artigo de desempenho.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações consulte as [funcionalidades da versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
