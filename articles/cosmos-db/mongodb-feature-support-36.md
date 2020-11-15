---
title: A API da Azure Cosmos DB para a versão MongoDB (versão 3.6) suportada e sintaxe
description: Saiba mais sobre a API suportada pela Azure Cosmos DB para a versão MongoDB (versão 3.6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/07/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: bb9efa3fde0ed840589b66db7b28392de67ee8dd
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635624"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>API do Azure Cosmos DB para MongoDB (versão 3.6): sintaxe e funcionalidades suportadas
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode comunicar com a API da Azure Cosmos DB para a MongoDB usando qualquer um dos [motoristas](https://docs.mongodb.org/ecosystem/drivers)de clientes de código aberto MongoDB . A API do Azure Cosmos DB para MongoDB permite a utilização dos controladores cliente existentes através do [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Ao utilizar a API da Azure Cosmos para a MongoDB, poderá usufruir dos benefícios do MongoDB a que está habituado, com todas as capacidades empresariais que a Cosmos DB fornece: [distribuição global, fragmentos](distribute-data-globally.md) [automáticos,](partitioning-overview.md)disponibilidade e garantias de latência, encriptação em repouso, backups e muito mais.

## <a name="protocol-support"></a>Apoio ao Protocolo

A API do Azure Cosmos DB para MongoDB é compatível com a versão  **3.6** do servidor MongoDB por predefinição para as novas contas. Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. Qualquer controlador do cliente que compreenda estes protocolos deverá conseguir ligar à API do Azure Cosmos DB para MongoDB. Observe que, ao usar a API do Azure Cosmos DB para contas do MongoDB, a versão 3.6 das contas tem o ponto final no formato `*.mongo.cosmos.azure.com`, enquanto a versão 3.2 das contas tem o ponto final no formato `*.documents.azure.com`.

## <a name="query-language-support"></a>Suporte linguístico de consulta

A API da Azure Cosmos DB para o MongoDB fornece um suporte abrangente para construções linguísticas de consulta mongoDB. Abaixo pode encontrar a lista detalhada de operações apoiadas atualmente, operadores, etapas, comandos e opções.

## <a name="database-commands"></a>Comandos da base de dados

A API da Azure Cosmos DB para a MongoDB suporta os seguintes comandos de base de dados:

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita

|Comando  |Suportado |
|---------|---------|
|delete | Yes |
|find | Yes     |
|findAndModify | Yes  |
|getLastError|   Yes |
|getMore  |  Yes  |
|obterPrevError | Não  |
|insert  |   Yes  |
|paraleloCollectionScan  | Yes   |
|resetEror |    Não  |
|update  |   Yes  |
|[Alterar fluxos](mongodb-change-streams.md)  |  Yes  |
|GridFS |   Yes  |

### <a name="authentication-commands"></a>Comandos de autenticação

|Comando  |Suportado |
|---------|---------|
|authenticate    |   Yes      |
|logout    |      Yes   |
|getnonce   |    Yes     |


### <a name="administration-commands"></a>Comandos de administração

|Comando  |Suportado |
|---------|---------|
|Coleções Cobertas   |   Não      |
|cloneCollectionAsCapped     |   Não      |
|collMod     |   Não      |
|collMod: expiraafterSeconds   |   Não      |
|converteToCapped   |  Não       |
|copydb     |  Não       |
|criar   |    Yes     |
|createIndexes     |  Yes       |
|atualOp     |  Yes       |
|drop     |   Yes      |
|dropDatabase     |  Yes       |
|dropIndexes     |   Yes      |
|filemd5    |   Yes      |
|killCursors    |  Yes       |
|killOp     |   Não      |
|listCollections     |  Yes       |
|listDatabases     |  Yes       |
|listIndexes     |  Yes       |
|reIndex     |    Yes     |
|renomeCollecto     |    Não     |
|connectionStatus    |     Não    |

### <a name="diagnostics-commands"></a>Comandos de diagnóstico

|Comando  |Suportado |
|---------|---------|
|buildInfo         |   Yes      |
|collStats    |  Yes       |
|ConnPoolStats     |  Não       |
|connectionStatus     |  Não       |
|dataSize     |   Não      |
|dbHash    |    Não     |
|dbStats     |   Yes      |
|explicar     |   Yes      |
|explicar: execuçõesStats     |   Yes      |
|funcionalidades     |    Não     |
|hostInfo     |   Não      |
|listDatabases         |   Yes      |
|listCommands     |  Não       |
|perfis     |  Não       |
|serverStatus     |  Não       |
|top     |    Não     |
|whatsmyuri     |   Yes      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

### <a name="aggregation-commands"></a>Comandos de agregação

|Comando  |Suportado |
|---------|---------|
|aggregate |   Yes  |
|count     |   Yes  |
|distinct  | Yes |
|mapReduce | Não |

### <a name="aggregation-stages"></a>Fases de agregação

|Comando  |Suportado |
|---------|---------|
|$collStats    |Não|
|$project    |Yes|
|$match    |Yes|
|$redact|    Yes|
|$limit    |Yes|
|$skip    |Yes|
|$unwind|    Yes|
|$group    |    Yes|
|$sample|        Yes|
|$sort    |Yes|
|$geoNear|    Não|
|$lookup    |    Yes|
|$out        |Yes|
|$indexStats|        Não|
|$facet    |Yes|
|$bucket|    Não|
|$bucketAuto|    Não|
|$sortByCount|    Yes|
|$addFields    |Yes|
|$replaceRoot|    Yes|
|$count    |Yes|
|$currentOp|    Não|
|$listLocalSessions    |Não|
|$listSessions    |Não|
|$graphLookup    |Yes|

### <a name="boolean-expressions"></a>Expressões booleanas

|Comando  |Suportado |
|---------|---------|
|$and| Yes|
|$or|Yes|
|$not|Yes|

### <a name="set-expressions"></a>Expressões de definição

|Comando  |Suportado |
|---------|---------|
| $setEquals | Yes|
|$setIntersection|Yes|
| $setUnion|Yes|
| $setDifference|Yes|
| $setIsSubset|Yes|
| $anyElementTrue|Yes|
| $allElementsTrue|Yes|

### <a name="comparison-expressions"></a>Expressões de comparação

|Comando  |Suportado |
|---------|---------|
|$cmp     |  Yes       |
|$eq|    Yes| 
|$gt |    Yes| 
|$gte|    Yes| 
|$lt    |Yes|
|$lte|    Yes| 
|$ne    |    Yes| 
|$in    |    Yes| 
|$nin    |    Yes| 

### <a name="arithmetic-expressions"></a>Expressões aritméticas

|Comando  |Suportado |
|---------|---------|
|$abs |  Yes       |
| $add |  Yes       |
| $ceil |  Yes       |
| $divide |  Yes       |
| $exp |  Yes       |
| $floor |  Yes       |
| $ln |  Yes       |
| $log |  Yes       |
| $log10 |  Yes       |
| $mod |  Yes       |
| $multiply |  Yes       |
| $pow |  Yes       |
| $sqrt |  Yes       |
| $subtract |  Yes       |
| $trunc |  Yes       |

### <a name="string-expressions"></a>Expressões de cadeia

|Comando  |Suportado |
|---------|---------|
|$concat |  Yes       |
| $indexOfBytes|  Yes       |
| $indexOfCP|  Yes       |
| $split|  Yes       |
| $strLenBytes|  Yes       |
| $strLenCP|  Yes       |
| $strcasecmp|  Yes       |
| $substr|  Yes       |
| $substrBytes|  Yes       |
| $substrCP|  Yes       |
| $toLower|  Yes       |
| $toUpper|  Yes       |

### <a name="text-search-operator"></a>Operador de pesquisa de texto

|Comando  |Suportado |
|---------|---------|
| $meta | Não|

### <a name="array-expressions"></a>Expressões de matriz

|Comando  |Suportado |
|---------|---------|
|$arrayElemAt    |    Yes|
|$arrayToObject|    Yes|
|$concatArrays    |    Yes|
|$filter    |    Yes|
|$indexOfArray    |Yes|
|$isArray    |    Yes|
|$objectToArray    |Yes|
|$range    |Yes|
|$reverseArray    |    Yes|
|$reduce|    Yes|
|$size    |    Yes|
|$slice    |    Yes|
|$zip    |    Yes|
|$in    |    Yes|

### <a name="variable-operators"></a>Operadores variáveis

|Comando  |Suportado |
|---------|---------|
|$map    |Não|
|$let    |Yes|

### <a name="system-variables"></a>Variáveis do sistema

|Comando  |Suportado |
|---------|---------|
|$$CURRENT|    Yes|
|$$DESCEND|        Yes|
|$$KEEP        |Yes|
|$$PRUNE    |    Yes|
|$$REMOVE    |Yes|
|$$ROOT        |Yes|

### <a name="literal-operator"></a>Operador literal

|Comando  |Suportado |
|---------|---------|
|$literal    |Yes|

### <a name="date-expressions"></a>Expressões de data

|Comando  |Suportado |
|---------|---------|
|$dayOfYear    |Yes    |
|$dayOfMonth|    Yes    |
|$dayOfWeek    |Yes    |
|$year    |Yes    |
|$month    |Yes|    
|$week    |Yes    |
|$hour    |Yes    |
|$minute|    Yes|    
|$second    |Yes    |
|$millisecond|    Yes|    
|$dateToString    |Yes    |
|$isoDayOfWeek    |Yes    |
|$isoWeek    |Yes    |
|$dateFromParts|    Não|    
|$dateToParts    |Não    |
|$dateFromString|    Não|
|$isoWeekYear    |Yes    |

### <a name="conditional-expressions"></a>Expressões condicionais

|Comando  |Suportado |
|---------|---------|
| $cond| Yes|
| $ifNull| Yes|
| $switch |Yes|

### <a name="data-type-operator"></a>Operador de tipo de dados

|Comando  |Suportado |
|---------|---------|
| $type| Yes|

### <a name="accumulator-expressions"></a>Expressões acumuladoras

|Comando  |Suportado |
|---------|---------|
|$sum    |Yes    |
|$avg    |Yes    |
|$first|    Yes|
|$last    |Yes    |
|$max    |Yes    |
|$min    |Yes    |
|$push|    Yes|
|$addToSet|    Yes|
|$stdDevPop|    Não    |
|$stdDevSamp|    Não|

### <a name="merge-operator"></a>Operador de fusão

|Comando  |Suportado |
|---------|---------|
| $mergeObjects | Yes|

## <a name="data-types"></a>Tipos de dados

|Comando  |Suportado |
|---------|---------|
|Double (Duplo)    |Sim    |
|String    |Yes    |
|Objeto    |Yes    |
|Matriz    |Yes    |
|Dados Binários    |Yes|    
|ObjectId    |Yes    |
|Booleano    |Yes    |
|Data    |Yes    |
|Nulo    |Yes    |
|Inteiro de 32 bits (int)    |Yes    |
|Timestamp    |Yes    |
|64-bit Inteiro (longo)    |Yes    |
|MinKey    |Yes    |
|MaxKey    |Yes    |
|Decimal128    |Yes|    
|Expressão Regular    |Yes|
|JavaScript    |Yes|
|JavaScript (com âmbito)|    Yes    |
|Indefinido    |Yes    |

## <a name="indexes-and-index-properties"></a>Índices e propriedades de índice

### <a name="indexes"></a>Índices

|Comando  |Suportado |
|---------|---------|
|Índice de Campo Único    |Yes    |
|Índice Composto    |Yes    |
|Índice Multikey    |Yes    |
|Índice de Texto    |Não|
|2desfera    |Yes    |
|Índice 2d    |Não    |
|Índice de Haxixe    | Yes|

### <a name="index-properties"></a>Propriedades de índice

|Comando  |Suportado |
|---------|---------|
|TTL|    Yes    |
|Exclusivo    |Yes|
|Parcial|    Não|
|Caso Insensível    |Não|
|Disperso    |Não |
|Fundo|    Yes |

## <a name="operators"></a>Operadores

### <a name="logical-operators"></a>Operadores lógicos

|Comando  |Suportado |
|---------|---------|
|$or    |    Yes|
|$and    |    Yes|
|$not    |    Yes|
|$nor    |    Yes| 

### <a name="element-operators"></a>Operadores de elementos

|Comando  |Suportado |
|---------|---------|
|$exists|    Yes|
|$type    |    Yes|

### <a name="evaluation-query-operators"></a>Operadores de consulta de avaliação

|Comando  |Suportado |
|---------|---------|
|$expr    |    Não|
|$jsonSchema    |    Não|
|$mod    |    Yes|
|$regex |    Yes|
|$text    | Não (Não suportado. Use $regex em vez disso.)| 
|$where    |Não| 

Nas consultas $regex, expressões ancoradas à esquerda permitem a procura de índices. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.

Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex. Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})``` - tem de ser modificada da seguinte forma:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas. O operador barra "|" atua como uma função "or" – a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operadores de matriz

|Comando  |Suportado | 
|---------|---------|
| $all | Yes| 
| $elemMatch | Yes| 
| $size | Yes | 

### <a name="comment-operator"></a>Operador de comentários

|Comando  |Suportado | 
|---------|---------|
$comment |Yes| 

### <a name="projection-operators"></a>Operadores de projeção

|Comando  |Suportado |
|---------|---------|
|$elemMatch    |Yes|
|$meta|    Não|
|$slice    | Yes|

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo

|Comando  |Suportado |
|---------|---------|
|$inc    |    Yes|
|$mul    |    Yes|
|$rename    |    Yes|
|$setOnInsert|    Yes|
|$set    |Yes|
|$unset| Yes|
|$min    |Yes|
|$max    |Yes|
|$currentDate    | Yes|

#### <a name="array-update-operators"></a>Operadores de atualização de matriz

|Comando  |Suportado |
|---------|---------|
|$    |Yes|
|$[]|    Yes|
|$[<identifier>]|    Yes|
|$addToSet    |Yes|
|$pop    |Yes|
|$pullAll|    Yes|
|$pull    |Yes|
|$push    |Yes|
|$pushAll| Yes|


#### <a name="update-modifiers"></a>Atualizar modificadores

|Comando  |Suportado |
|---------|---------|
|$each    |    Yes|
|$slice    |Yes|
|$sort    |Yes|
|$position    |Yes|

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit

|Comando  |Suportado |
|---------|---------|
| $bit    |    Yes|    
|$bitsAllSet    |    Não|
|$bitsAnySet    |    Não|
|$bitsAllClear    |Não|
|$bitsAnyClear    |Não|

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Suportado| 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near |  Yes |
$nearSphere |  Yes |
$geometry |  Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | Não |
$centerSphere | Não |
$box | Não |
$polygon |  Não |

## <a name="cursor-methods"></a>Métodos de cursor

|Comando  |Suportado |
|---------|---------|
|cursor.batchSize()    |    Yes|
|cursor.close()    |Yes|
|cursor.isClosed()|        Yes|
|cursor.collation()|    Não|
|cursor.comment()    |Yes|
|cursor.count()    |Yes|
|cursor.explain()|    Não|
|cursor.forEach()    |Yes|
|cursor.hasNext()    |Yes|
|cursor.hint()    |Yes|
|cursor.isExhausted()|    Yes|
|cursor.itcount()    |Yes|
|cursor.limit()    |Yes|
|cursor.map()    |Yes|
|cursor.maxScan()    |Yes|
|cursor.maxTimeMS()|    Yes|
|cursor.max()    |Yes|
|cursor.min()    |Yes|
|cursor.next()|    Yes|
|cursor.noCursorTimeout()    |Não|
|cursor.objsLeftInBatch()    |Yes|
|cursor.pretty()|    Yes|
|cursor.readConcern()|    Yes|
|cursor.readPref()        |Yes|
|cursor.returnKey()    |Não|
|cursor.showRecordId()|    Não|
|cursor.size()    |Yes|
|cursor.skip()    |Yes|
|cursor.sort()    |    Yes|
|cursor.tailable()|    Não|
|cursor.toArray()    |Yes|

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

## <a name="time-to-live-ttl"></a>TTL

Cosmos DB suporta um time-to-live (TTL) baseado no tempotamp do documento. A TTL pode ser ativada para coleções indo ao [portal Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

A Cosmos DB ainda não suporta utilizadores e funções. No entanto, a Cosmos DB suporta o controlo de acesso baseado em funções (Azure RBAC) e palavras-passe/chaves só de leitura e leitura que podem ser obtidas através do [portal Azure](https://portal.azure.com) (página de cadeia de ligação).

## <a name="replication"></a>Replicação

Cosmos DB suporta replicação automática e nativa nas camadas mais baixas. Esta lógica é expandida para conseguir também a replicação global de latência baixa. Cosmos DB não suporta comandos de replicação manual.

## <a name="write-concern"></a>Preocupação Escrita

Algumas aplicações baseiam-se numa [Comissão de Escrita](https://docs.mongodb.com/manual/reference/write-concern/) que especifica o número de respostas necessárias durante uma operação de escrita. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as escritas são automaticamente Quórum por predefinição. Qualquer preocupação de escrita especificada pelo código do cliente é ignorada. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho).

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB suporta a fragmentação automática do lado do servidor. Gere a criação, colocação e equilíbrio de fragmentos automaticamente. A Azure Cosmos DB não suporta comandos manuais de sharding, o que significa que não tem de invocar comandos como addShard, balancerStart, moveChunk etc. Basta especificar a chave de fragmentos enquanto cria os recipientes ou consulta os dados.

## <a name="sessions"></a>Sessões

A Azure Cosmos DB ainda não suporta comandos de sessões laterais do servidor.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações consulte as [funcionalidades da versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.

<sup>Nota: Este artigo descreve uma característica da Azure Cosmos DB que fornece compatibilidade de protocolos de fio com bases de dados MongoDB. A Microsoft não gere bases de dados mongoDB para fornecer este serviço. AZure Cosmos DB não é afiliado à MongoDB, Inc.</sup>
