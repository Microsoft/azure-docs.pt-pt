---
title: Versão 4.0 servidor suportada funcionalidades e sintaxe na API da Azure Cosmos DB para o MongoDB
description: Saiba mais sobre a API da Azure Cosmos para a versão do servidor MongoDB 4.0 suportada e sintaxe. Conheça os comandos da base de dados, suporte de linguagem de consulta, tipos de dados, comandos de gasoduto de agregação e operadores suportados.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9eebc77c5b3d9402c766320fddfdaf05d50b574f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485406"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>A API da Azure Cosmos DB para MongoDB (versão do servidor 4.0): funcionalidades suportadas e sintaxe
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode comunicar com a API da Azure Cosmos DB para a MongoDB usando qualquer um dos [motoristas](https://docs.mongodb.org/ecosystem/drivers)de clientes de código aberto MongoDB . A API do Azure Cosmos DB para MongoDB permite a utilização dos controladores cliente existentes através do [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Ao utilizar a API da Azure Cosmos para a MongoDB, poderá usufruir dos benefícios do MongoDB a que está habituado, com todas as capacidades empresariais que a Cosmos DB fornece: [distribuição global, fragmentos](distribute-data-globally.md) [automáticos,](partitioning-overview.md)disponibilidade e garantias de latência, encriptação em repouso, backups e muito mais.

## <a name="protocol-support"></a>Apoio ao Protocolo

Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. Qualquer controlador do cliente que compreenda estes protocolos deverá conseguir ligar à API do Azure Cosmos DB para MongoDB. Ao utilizar a API da Azure Cosmos para contas MongoDB, as versões de contas 3.6+ têm o ponto final no `*.mongo.cosmos.azure.com` formato, enquanto a versão 3.2 das contas tem o ponto final no formato `*.documents.azure.com` .

> [!NOTE]
> Este artigo apenas lista os comandos do servidor suportado e exclui as funções de invólucro do lado do cliente. Funções de invólucro do lado do cliente, tais como `deleteMany()` e `updateMany()` utilizar internamente os `delete()` comandos e `update()` servidor. As funções que utilizam comandos de servidor suportados são compatíveis com a API da Azure Cosmos DB para o MongoDB.

## <a name="query-language-support"></a>Suporte linguístico de consulta

A API da Azure Cosmos DB para o MongoDB fornece um suporte abrangente para construções linguísticas de consulta mongoDB. Abaixo pode encontrar a lista detalhada de operações apoiadas atualmente, operadores, etapas, comandos e opções.

## <a name="database-commands"></a>Comandos da base de dados

A API da Azure Cosmos DB para a MongoDB suporta os seguintes comandos de base de dados:

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita

| Comando | Suportado |
|---------|---------|
| [mudar fluxos](mongodb-change-streams.md) | Yes |
| delete | Yes |
| eval | No |
| find | Yes |
| findAndModify | Yes |
| getLastError | Yes |
| getMore | Yes |
| obterPrevError | No |
| insert | Yes |
| paraleloCollectionScan | No |
| resetEror | No |
| update | Yes |

### <a name="transaction-commands"></a>Comandos de transação

| Comando | Suportado |
|---------|---------|
| abortTransação | Yes |
| cometerTranscção | Yes |

### <a name="authentication-commands"></a>Comandos de autenticação

| Comando | Suportado |
|---------|---------|
| authenticate | Yes |
| getnonce | Yes |
| logout | Yes |

### <a name="administration-commands"></a>Comandos de administração

| Comando | Suportado |
|---------|---------|
| cloneCollectionAsCapped | No |
| collMod | No |
| connectionStatus | No |
| converteToCapped | No |
| copydb | No |
| criar | Yes |
| createIndexes | Yes |
| atualOp | Yes |
| drop | Yes |
| dropDatabase | Yes |
| dropIndexes | Yes |
| filemd5 | Yes |
| killCursors | Yes |
| killOp | No |
| listCollections | Yes |
| listDatabases | Yes |
| listIndexes | Yes |
| reIndex | Yes |
| renomeCollecto | No |

### <a name="diagnostics-commands"></a>Comandos de diagnóstico

| Comando | Suportado |
|---------|---------|
| buildInfo | Yes |
| collStats | Yes |
| ConnPoolStats | No |
| connectionStatus | No |
| dataSize | No |
| dbHash | No |
| dbStats | Yes |
| explicar | Yes |
| funcionalidades | No |
| hostInfo | Yes |
| listDatabases | Yes |
| listCommands | No |
| perfis | No |
| serverStatus | No |
| top | No |
| whatsmyuri | Yes |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Gasoduto de agregação

### <a name="aggregation-commands"></a>Comandos de agregação

| Comando | Suportado |
|---------|---------|
| aggregate | Yes |
| count | Yes |
| distinct | Yes |
| mapReduce | No |

### <a name="aggregation-stages"></a>Fases de agregação

| Comando | Suportado |
|---------|---------|
| $addFields | Yes |
| $bucket | No |
| $bucketAuto | No |
| $changeStream | Yes |
| $collStats | No |
| $count | Yes |
| $currentOp | No |
| $facet | Yes |
| $geoNear | Yes |
| $graphLookup | Yes |
| $group | Yes |
| $indexStats | No |
| $limit | Yes |
| $listLocalSessions | No |
| $listSessions | No |
| $lookup | Yes |
| $match | Yes |
| $out | Yes |
| $project | Yes |
| $redact | Yes |
| $replaceRoot | Yes |
| $replaceWith | No |
| $sample | Yes |
| $skip | Yes |
| $sort | Yes |
| $sortByCount | Yes |
| $unwind | Yes |

### <a name="boolean-expressions"></a>Expressões booleanas

| Comando | Suportado |
|---------|---------|
| $and | Yes |
| $not | Yes |
| $or | Yes |

### <a name="conversion-expressions"></a>Expressões de conversão

| Comando | Suportado |
|---------|---------|
| $convert | Yes |
| $toBool | Yes |
| $toDate | Yes |
| $toDecimal | Yes |
| $toDouble | Yes |
| $toInt | Yes |
| $toLong | Yes |
| $toObjectId | Yes |
| $toString | Yes |

### <a name="set-expressions"></a>Expressões de definição

| Comando | Suportado |
|---------|---------|
| $setEquals | Yes |
| $setIntersection | Yes |
| $setUnion | Yes |
| $setDifference | Yes |
| $setIsSubset | Yes |
| $anyElementTrue | Yes |
| $allElementsTrue | Yes |

### <a name="comparison-expressions"></a>Expressões de comparação

| Comando | Suportado |
|---------|---------|
| $cmp | Yes |
| $eq | Yes | 
| $gt | Yes | 
| $gte | Yes | 
| $lt | Yes |
| $lte | Yes | 
| $ne | Yes | 
| $in | Yes | 
| $nin | Yes | 

### <a name="arithmetic-expressions"></a>Expressões aritméticas

| Comando | Suportado |
|---------|---------|
| $abs | Yes |
| $add | Yes |
| $ceil | Yes |
| $divide | Yes |
| $exp | Yes |
| $floor | Yes |
| $ln | Yes |
| $log | Yes |
| $log10 | Yes |
| $mod | Yes |
| $multiply | Yes |
| $pow | Yes |
| $sqrt | Yes |
| $subtract | Yes |
| $trunc | Yes |

### <a name="string-expressions"></a>Expressões de cadeia

| Comando | Suportado |
|---------|---------|
| $concat | Yes |
| $indexOfBytes | Yes |
| $indexOfCP | Yes |
| $ltrim | Yes |
| $rtrim | Yes |
| $trim | Yes |
| $split | Yes |
| $strLenBytes | Yes |
| $strLenCP | Yes |
| $strcasecmp | Yes |
| $substr | Yes |
| $substrBytes | Yes |
| $substrCP | Yes |
| $toLower | Yes |
| $toUpper | Yes |

### <a name="text-search-operator"></a>Operador de pesquisa de texto

| Comando | Suportado |
|---------|---------|
| $meta | No |

### <a name="array-expressions"></a>Expressões de matriz

| Comando | Suportado |
|---------|---------|
| $arrayElemAt | Yes |
| $arrayToObject | Yes |
| $concatArrays | Yes |
| $filter | Yes |
| $indexOfArray | Yes |
| $isArray | Yes |
| $objectToArray | Yes |
| $range | Yes |
| $reverseArray | Yes |
| $reduce | Yes |
| $size | Yes |
| $slice | Yes |
| $zip | Yes |
| $in | Yes |

### <a name="variable-operators"></a>Operadores variáveis

| Comando | Suportado |
|---------|---------|
| $map | Yes |
| $let | Yes |

### <a name="system-variables"></a>Variáveis do sistema

| Comando | Suportado |
|---------|---------|
| $$CURRENT | Yes |
| $$DESCEND | Yes |
| $$KEEP | Yes |
| $$PRUNE | Yes |
| $$REMOVE | Yes |
| $$ROOT | Yes |

### <a name="literal-operator"></a>Operador literal

| Comando | Suportado |
|---------|---------|
| $literal | Yes |

### <a name="date-expressions"></a>Expressões de data

| Comando | Suportado |
|---------|---------|
| $dayOfYear | Yes |
| $dayOfMonth | Yes |
| $dayOfWeek | Yes |
| $year | Yes |
| $month | Yes | 
| $week | Yes |
| $hour | Yes |
| $minute | Yes | 
| $second | Yes |
| $millisecond | Yes | 
| $dateToString | Yes |
| $isoDayOfWeek | Yes |
| $isoWeek | Yes |
| $dateFromParts | No | 
| $dateToParts | No |
| $dateFromString | No |
| $isoWeekYear | Yes |

### <a name="conditional-expressions"></a>Expressões condicionais

| Comando | Suportado |
|---------|---------|
| $cond | Yes |
| $ifNull | Yes |
| $switch | Yes |

### <a name="data-type-operator"></a>Operador de tipo de dados

| Comando | Suportado |
|---------|---------|
| $type | Yes |

### <a name="accumulator-expressions"></a>Expressões acumuladoras

| Comando | Suportado |
|---------|---------|
| $sum | Yes |
| $avg | Yes |
| $first | Yes |
| $last | Yes |
| $max | Yes |
| $min | Yes |
| $push | Yes |
| $addToSet | Yes |
| $stdDevPop | Yes |
| $stdDevSamp | Yes |

### <a name="merge-operator"></a>Operador de fusão

| Comando | Suportado |
|---------|---------|
| $mergeObjects | Yes |

## <a name="data-types"></a>Tipos de dados

A API da Azure Cosmos DB para a MongoDB suporta documentos codificados no formato BSON mongodb. A versão API 4.0 melhora o uso interno deste formato para melhorar o desempenho e reduzir custos. Os documentos escritos ou atualizados através de um ponto final que executa 4.0 beneficiam disto.
 
Num [cenário de atualização,](mongodb-version-upgrade.md)os documentos escritos antes da atualização para a versão 4.0 não beneficiarão do desempenho melhorado até serem atualizados através de uma operação de escrita através do ponto final 4.0.

| Comando | Suportado |
|---------|---------|
| Double (Duplo) | Sim |
| String | Yes |
| Objeto | Yes |
| Matriz | Yes |
| Dados Binários | Yes | 
| ObjectId | Yes |
| Booleano | Yes |
| Data | Yes |
| Nulo | Yes |
| Inteiro de 32 bits (int) | Yes |
| CarimboDeDataEHora | Yes |
| 64-bit Inteiro (longo) | Yes |
| MinKey | Yes |
| MaxKey | Yes |
| Decimal128 | Yes | 
| Expressão Regular | Yes |
| JavaScript | Yes |
| JavaScript (com âmbito)| Yes |
| Indefinido | Yes |

## <a name="indexes-and-index-properties"></a>Índices e propriedades de índice

### <a name="indexes"></a>Índices

| Comando | Suportado |
|---------|---------|
| Índice de Campo Único | Yes |
| Índice Composto | Yes |
| Índice Multikey | Yes |
| Índice de Texto | No |
| 2desfera | Yes |
| Índice 2d | No |
| Índice de Haxixe | Yes |

### <a name="index-properties"></a>Propriedades de índice

| Comando | Suportado |
|---------|---------|
| TTL | Yes |
| Exclusivo | Yes |
| Parcial | No |
| Caso Insensível | No |
| Disperso | No |
| Fundo | Yes |

## <a name="operators"></a>Operadores

### <a name="logical-operators"></a>Operadores lógicos

| Comando | Suportado |
|---------|---------|
| $or | Yes |
| $and | Yes |
| $not | Yes |
| $nor | Yes | 

### <a name="element-operators"></a>Operadores de elementos

| Comando | Suportado |
|---------|---------|
| $exists | Yes |
| $type | Yes |

### <a name="evaluation-query-operators"></a>Operadores de consulta de avaliação

| Comando | Suportado |
|---------|---------|
| $expr | No |
| $jsonSchema | No |
| $mod | Yes |
| $regex | Yes |
| $text | Não (Não suportado. Use $regex em vez disso.)| 
| $where | No | 

Nas consultas $regex, expressões ancoradas à esquerda permitem a procura de índices. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.

Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex. Por exemplo, dada a seguinte consulta original: `find({x:{$regex: /^abc$/})` - tem de ser modificada da seguinte forma:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas. O operador barra "|" atua como uma função "or" – a consulta `find({x:{$regex: /^abc |^def/})` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Operadores de matriz

| Comando | Suportado | 
|---------|---------|
| $all | Yes | 
| $elemMatch | Yes | 
| $size | Yes | 

### <a name="comment-operator"></a>Operador de comentários

| Comando | Suportado | 
|---------|---------|
| $comment | Yes | 

### <a name="projection-operators"></a>Operadores de projeção

| Comando | Suportado |
|---------|---------|
| $elemMatch | Yes |
| $meta | No |
| $slice | Yes |

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo

| Comando | Suportado |
|---------|---------|
| $inc | Yes |
| $mul | Yes |
| $rename | Yes |
| $setOnInsert | Yes |
| $set | Yes |
| $unset | Yes |
| $min | Yes |
| $max | Yes |
| $currentDate | Yes |

#### <a name="array-update-operators"></a>Operadores de atualização de matriz

| Comando | Suportado |
|---------|---------|
| $ | Yes |
| $[]| Yes |
| $[<identifier>]| Yes |
| $addToSet | Yes |
| $pop | Yes |
| $pullAll | Yes |
| $pull | Yes |
| $push | Yes |
| $pushAll | Yes |

#### <a name="update-modifiers"></a>Atualizar modificadores

| Comando | Suportado |
|---------|---------|
| $each | Yes |
| $slice | Yes |
| $sort | Yes |
| $position | Yes |

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit

| Comando | Suportado |
|---------|---------|
| $bit | Yes | 
| $bitsAllSet | No |
| $bitsAnySet | No |
| $bitsAllClear | No |
| $bitsAnyClear | No |

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Suportado | 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near | Yes |
$nearSphere | Yes |
$geometry | Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | No |
$centerSphere | No |
$box | No |
$polygon | No |

## <a name="sort-operations"></a>Ordenar operações

Ao utilizar a `findOneAndUpdate` operação, as operações de classificação num único campo são suportadas, mas as operações de classificação em vários campos não são suportadas.

## <a name="unique-indexes"></a>Índices exclusivos

[Índices únicos](mongodb-indexing.md#unique-indexes) garantem que um campo específico não tem valores duplicados em todos os documentos de uma coleção, semelhante à forma como a singularidade é preservada na chave "_id" padrão. Pode criar índices únicos em Azure Cosmos DB utilizando o `createIndex` comando com o parâmetro de `unique` restrição:

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

[Os índices compostos](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) fornecem uma forma de criar um índice para grupos de campos para até oito campos. Este tipo de índice difere dos índices compostos mongoDB nativos. Em Azure Cosmos DB, os índices compostos são usados para operações de triagem que são aplicadas em vários campos. Para criar um índice composto, é necessário especificar mais do que uma propriedade como parâmetro:

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

A Azure Cosmos DB suporta o GridFS através de qualquer controlador Mongo compatível com GridFS.

## <a name="replication"></a>Replicação

O Azure Cosmos DB suporta a replicação nativa e automática nas camadas inferiores. Esta lógica é expandida para conseguir também a replicação global de latência baixa. Cosmos DB não suporta comandos de replicação manual.

## <a name="retryable-writes"></a>Redações Retripáveis

Cosmos DB ainda não suporta redações retripáveis. Os controladores do cliente devem adicionar o parâmetro URL 'retryWrites=falso' à sua cadeia de ligação. Mais parâmetros URL podem ser adicionados prefixando-os com um '&'. 

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB suporta a fragmentação automática do lado do servidor. Gere a criação, colocação e equilíbrio de fragmentos automaticamente. A Azure Cosmos DB não suporta comandos manuais de sharding, o que significa que não tem de invocar comandos como addShard, balancerStart, moveChunk etc. Basta especificar a chave de fragmentos enquanto cria os recipientes ou consulta os dados.

## <a name="sessions"></a>Sessões

A Azure Cosmos DB ainda não suporta comandos de sessões do lado do servidor.

## <a name="time-to-live-ttl"></a>TTL

A Azure Cosmos DB suporta um time-to-live (TTL) baseado no tempotamp do documento. A TTL pode ser ativada para coleções indo ao [portal Azure](https://portal.azure.com).

## <a name="transactions"></a>Transações

As transações multi-documentos são suportadas numa coleção nãohardificada. As transações multi-documentos não são suportadas através de coleções ou em coleções de fragmentos. O tempo limite para transações é de 5 segundos fixos.

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

O Azure Cosmos DB ainda não suporta utilizadores e funções. No entanto, a Cosmos DB suporta o controlo de acesso baseado em funções (Azure RBAC) e palavras-passe/chaves só de leitura e leitura que podem ser obtidas através do [portal Azure](https://portal.azure.com) (página de cadeia de ligação).

## <a name="write-concern"></a>Preocupação Escrita

Algumas aplicações baseiam-se numa [Comissão de Escrita,](https://docs.mongodb.com/manual/reference/write-concern/)que especifica o número de respostas necessárias durante uma operação de escrita. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as escritas são automaticamente Quórum por predefinição. Qualquer preocupação de escrita especificada pelo código do cliente é ignorada. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho).

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
