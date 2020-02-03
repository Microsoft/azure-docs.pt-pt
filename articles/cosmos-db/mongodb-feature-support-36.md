---
title: API do Azure Cosmos DB para recursos e sintaxe do MongoDB (versão 3,6) com suporte
description: Saiba mais sobre a API de Azure Cosmos DB para os recursos e a sintaxe do MongoDB (versão 3,6) com suporte.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: a32affab45ab99a89113644bb08c4f2b57d69018
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721018"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>API do Azure Cosmos DB para MongoDB (versão 3,6): recursos e sintaxe com suporte

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API da Azure Cosmos DB para mongoDB utilizando qualquer um dos [condutores](https://docs.mongodb.org/ecosystem/drivers)de clientes MongoDB de código aberto. A API da Azure Cosmos DB para a MongoDB permite a utilização de condutores de clientes existentes, aderindo ao [protocolo de arame](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)MongoDB.

Ao utilizar a API do Azure Cosmos DB para mongoDB, pode usufruir dos benefícios do MongoDB a que está habituado, com todas as capacidades empresariais que a Cosmos DB fornece: [distribuição global,](distribute-data-globally.md) [sharding automático,](partition-data.md)garantias de disponibilidade e latência, indexação automática de cada campo, encriptação em repouso, backups e muito mais.

## <a name="protocol-support"></a>Suporte de protocolo

A API do Azure Cosmos DB para MongoDB é compatível com a versão **3.6** do servidor MongoDB por padrão para novas contas. Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. Qualquer controlador do cliente que compreenda estes protocolos deverá conseguir ligar à API do Azure Cosmos DB para MongoDB. Note que ao utilizar a API do Azure Cosmos DB para contas MongoDB, a versão 3.6 das contas tem o ponto final no formato `*.mongo.cosmos.azure.com` enquanto a versão 3.2 das contas tem o ponto final no formato `*.documents.azure.com`.

## <a name="query-language-support"></a>Suporte à linguagem de consulta

A API do Azure Cosmos DB para MongoDB fornece suporte abrangente para construções de linguagem de consulta do MongoDB. Abaixo pode encontrar a lista detalhada de operações, operadores, estágios, comandos e opções suportadas atualmente.

## <a name="database-commands"></a>Comandos da base de dados

A API do Azure Cosmos DB para MongoDB dá suporte aos seguintes comandos de banco de dados:

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita

|Comando  |Suportadas |
|---------|---------|
|delete | Sim |
|find | Sim     |
|findAndModify | Sim  |
|getLastError|   Sim |
|getMore  |  Sim  |
|getPrevError | Não  |
|insert  |   Sim  |
|paraleloCollectionScan  | Sim   |
|resetErro |   Não  |
|update  |   Sim  |
|[Alterar fluxos](mongodb-change-streams.md)  |  Sim  |
|Gridfs |   Sim  |

### <a name="authentication-commands"></a>Comandos de autenticação

|Comando  |Suportadas |
|---------|---------|
|authenticate    |   Sim      |
|logout    |      Sim   |
|getnonce   |    Sim     |


### <a name="administration-commands"></a>Comandos de administração

|Comando  |Suportadas |
|---------|---------|
|Coleções cobertas   |   Não      |
|cloneCollectionAsCapped     |   Não      |
|collMod     |   Não      |
|collMod: expiraafterSeconds   |   Não      |
|convertetoCapped   |  Não       |
|cópia     |  Não       |
|criar   |    Sim     |
|createIndexes     |  Sim       |
|currentOp     |  Sim       |
|drop     |   Sim      |
|dropDatabase     |  Sim       |
|dropIndexes     |   Sim      |
|filemd5    |   Sim      |
|killCursors    |  Sim       |
|killOp     |   Não      |
|listCollections     |  Sim       |
|listDatabases     |  Sim       |
|listIndexes     |  Sim       |
|reIndex     |    Sim     |
|renomeAde     |    Não     |
|connectionStatus    |     Não    |

### <a name="diagnostics-commands"></a>Comandos de diagnóstico

|Comando  |Suportadas |
|---------|---------|
|buildInfo       |   Sim      |
|collStats    |  Sim       |
|connPoolStats     |  Não       |
|connectionStatus     |  Não       |
|dadosTamanho     |   Não      |
|dbHash    |    Não     |
|dbStats     |   Sim      |
|explicar     | Não        |
|explicar: execuçãoStats     |     Não    |
|características     |    Não     |
|hostInfo     |   Não      |
|listDatabases       |   Sim      |
|listComandos     |  Não       |
|perfilador     |  Não       |
|serverStatus     |  Não       |
|Início     |    Não     |
|whatsmyuri     |   Sim      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

### <a name="aggregation-commands"></a>Comandos de agregação

|Comando  |Suportadas |
|---------|---------|
|aggregate |   Sim  |
|count     |   Sim  |
|distinct  | Sim |
|mapaReduzir | Não |

### <a name="aggregation-stages"></a>Fases de agregação

|Comando  |Suportadas |
|---------|---------|
|$collStats |Não|
|$project   |Sim|
|$match |Sim|
|$redact|   Sim|
|$limit |Sim|
|$skip  |Sim|
|$unwind|   Sim|
|$group |   Sim|
|$sample|       Sim|
|$sort  |Sim|
|$geoNear|  Não|
|$lookup    |   Sim|
|$out       |Sim|
|$indexStats|       Não|
|$facet |Não|
|$bucket|   Não|
|$bucketAuto|   Não|
|$sortByCount|  Sim|
|$addFields |Sim|
|$replaceRoot|  Sim|
|$count |Sim|
|$currentOp|    Não|
|$listLocalSessions |Não|
|$listSessions  |Não|
|$graphLookup   |Não|

### <a name="boolean-expressions"></a>Expressões booleanas

|Comando  |Suportadas |
|---------|---------|
|$and| Sim|
|$or|Sim|
|$not|Sim|

### <a name="set-expressions"></a>Expressões de definição

|Comando  |Suportadas |
|---------|---------|
| $setEquals | Sim|
|$setIntersection|Sim|
| $setUnion|Sim|
| $setDifference|Sim|
| $setIsSubset|Sim|
| $anyElementTrue|Sim|
| $allElementsTrue|Sim|

### <a name="comparison-expressions"></a>Expressões de comparação

|Comando  |Suportadas |
|---------|---------|
|$cmp     |  Sim       |
|$eq|   Sim| 
|$gt |  Sim| 
|$gte|  Sim| 
|$lt    |Sim|
|$lte|  Sim| 
|$ne    |   Sim| 
|$in    |   Sim| 
|$nin   |   Sim| 

### <a name="arithmetic-expressions"></a>Expressões aritméticas

|Comando  |Suportadas |
|---------|---------|
|$abs |  Sim       |
| $add |  Sim       |
| $ceil |  Sim       |
| $divide |  Sim       |
| $exp |  Sim       |
| $floor |  Sim       |
| $ln |  Sim       |
| $log |  Sim       |
| $log10 |  Sim       |
| $mod |  Sim       |
| $multiply |  Sim       |
| $pow |  Sim       |
| $sqrt |  Sim       |
| $subtract |  Sim       |
| $trunc |  Sim       |

### <a name="string-expressions"></a>Expressões de cadeia

|Comando  |Suportadas |
|---------|---------|
|$concat |  Sim       |
| $indexOfBytes|  Sim       |
| $indexOfCP|  Sim       |
| $split|  Sim       |
| $strLenBytes|  Sim       |
| $strLenCP|  Sim       |
| $strcasecmp|  Sim       |
| $substr|  Sim       |
| $substrBytes|  Sim       |
| $substrCP|  Sim       |
| $toLower|  Sim       |
| $toUpper|  Sim       |

### <a name="text-search-operator"></a>Operador de pesquisa de texto

|Comando  |Suportadas |
|---------|---------|
| $meta | Não|

### <a name="array-expressions"></a>Expressões de matriz

|Comando  |Suportadas |
|---------|---------|
|$arrayElemAt   |   Sim|
|$arrayToObject|    Sim|
|$concatArrays  |   Sim|
|$filter    |   Sim|
|$indexOfArray  |Sim|
|$isArray   |   Sim|
|$objectToArray |Sim|
|$range |Sim|
|$reverseArray  |   Sim|
|$reduce|   Sim|
|$size  |   Sim|
|$slice |   Sim|
|$zip   |   Sim|
|$in    |   Sim|

### <a name="variable-operators"></a>Operadores variáveis

|Comando  |Suportadas |
|---------|---------|
|$map   |Não|
|$let   |Sim|

### <a name="system-variables"></a>Variáveis do sistema

|Comando  |Suportadas |
|---------|---------|
|$$CURRENT| Sim|
|$$DESCEND|     Sim|
|$$KEEP     |Sim|
|$$PRUNE    |   Sim|
|$$REMOVE   |Sim|
|$$ROOT     |Sim|

### <a name="literal-operator"></a>Operador literal

|Comando  |Suportadas |
|---------|---------|
|$literal   |Sim|

### <a name="date-expressions"></a>Expressões de data

|Comando  |Suportadas |
|---------|---------|
|$dayOfYear |Sim    |
|$dayOfMonth|   Sim |
|$dayOfWeek |Sim    |
|$year  |Sim    |
|$month |Sim|   
|$week  |Sim    |
|$hour  |Sim    |
|$minute|   Sim|    
|$second    |Sim    |
|$millisecond|  Sim|    
|$dateToString  |Sim    |
|$isoDayOfWeek  |Sim    |
|$isoWeek   |Sim    |
|$dateFromParts|    Não| 
|$dateToParts   |Não |
|$dateFromString|   Não|
|$isoWeekYear   |Sim    |

### <a name="conditional-expressions"></a>Expressões condicionais

|Comando  |Suportadas |
|---------|---------|
| $cond| Sim|
| $ifNull| Sim|
| $switch |Sim|

### <a name="data-type-operator"></a>Operador de tipo de dados

|Comando  |Suportadas |
|---------|---------|
| $type| Sim|

### <a name="accumulator-expressions"></a>Expressões de acumuladores

|Comando  |Suportadas |
|---------|---------|
|$sum   |Sim    |
|$avg   |Sim    |
|$first|    Sim|
|$last  |Sim    |
|$max   |Sim    |
|$min   |Sim    |
|$push| Sim|
|$addToSet| Sim|
|$stdDevPop|    Não  |
|$stdDevSamp|   Não|

### <a name="merge-operator"></a>Operador de fusão

|Comando  |Suportadas |
|---------|---------|
| $mergeObjects | Sim|

## <a name="data-types"></a>Tipos de dados

|Comando  |Suportadas |
|---------|---------|
|Valor de duplo |Sim    |
|String |Sim    |
|Objeto |Sim    |
|Array  |Sim    |
|Binary Data    |Sim|   
|Objectide   |Sim    |
|Booleano    |Sim    |
|Date   |Sim    |
|Null   |Sim    |
|32 bits Inteiro (int)   |Sim    |
|Carimbo de data/hora  |Sim    |
|64 bits Inteiro (longo)  |Sim    |
|MinKey |Sim    |
|MaxKey |Sim    |
|Decimal128 |Sim|   
|Expressão Regular |Sim|
|JavaScript |Sim|
|JavaScript (com âmbito)|   Sim |
|Indefinido  |Sim    |

## <a name="indexes-and-index-properties"></a>Índices e propriedades indexadas

### <a name="indexes"></a>Índices

|Comando  |Suportadas |
|---------|---------|
|Índice de Campo Único |Sim    |
|Índice composto |Sim    |
|Índice Multikey |Sim    |
|Índice de Texto |Não|
|2dsphere   |Sim    |
|Índice 2d   |Não |
|Índice Hashed   | Sim|

### <a name="index-properties"></a>Propriedades indexadas

|Comando  |Suportadas |
|---------|---------|
|TTL|   Sim |
|Exclusivo |Sim|
|Parcial|   Não|
|Caso Insensível   |Não|
|Dispersos |Não |
|Fundo|    Sim |

## <a name="operators"></a>Operadores

### <a name="logical-operators"></a>Operadores lógicos

|Comando  |Suportadas |
|---------|---------|
|$or    |   Sim|
|$and   |   Sim|
|$not   |   Sim|
|$nor   |   Sim| 

### <a name="element-operators"></a>Operadores de elementos

|Comando  |Suportadas |
|---------|---------|
|$exists|   Sim|
|$type  |   Sim|

### <a name="evaluation-query-operators"></a>Operadores de consulta de avaliação

|Comando  |Suportadas |
|---------|---------|
|$expr  |   Não|
|$jsonSchema    |   Não|
|$mod   |   Sim|
|$regex |   Sim|
|$text  | Não (Não suportado. Use $regex em vez disso.)| 
|$where |Não| 

Nas consultas $regex, expressões ancoradas à esquerda permitem a pesquisa de índices. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.

Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex. Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, tem de ser modificada da seguinte forma:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas. O operador barra "|" atua como uma função "or" – a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operadores de matriz

|Comando  |Suportadas | 
|---------|---------|
| $all | Sim| 
| $elemMatch | Sim| 
| $size | Sim | 

### <a name="comment-operator"></a>Operador de comentários

|Comando  |Suportadas | 
|---------|---------|
$comment |Sim| 

### <a name="projection-operators"></a>Operadores de projeção

|Comando  |Suportadas |
|---------|---------|
|$elemMatch |Sim|
|$meta| Não|
|$slice | Sim|

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo

|Comando  |Suportadas |
|---------|---------|
|$inc   |   Sim|
|$mul   |   Sim|
|$rename    |   Sim|
|$setOnInsert|  Sim|
|$set   |Sim|
|$unset| Sim|
|$min   |Sim|
|$max   |Sim|
|$currentDate   | Sim|

#### <a name="array-update-operators"></a>Operadores de atualização de matriz

|Comando  |Suportadas |
|---------|---------|
|$  |Sim|
|$[]|   Sim|
|$[<identifier>]|   Sim|
|$addToSet  |Sim|
|$pop   |Sim|
|$pullAll|  Sim|
|$pull  |Sim|
|$push  |Sim|
|$pushAll| Sim|


#### <a name="update-modifiers"></a>Modificars de atualização

|Comando  |Suportadas |
|---------|---------|
|$each  |   Sim|
|$slice |Sim|
|$sort  |Sim|
|$position  |Sim|

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit

|Comando  |Suportadas |
|---------|---------|
| $bit  |   Sim|    
|$bitsAllSet    |   Não|
|$bitsAnySet    |   Não|
|$bitsAllClear  |Não|
|$bitsAnyClear  |Não|

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Suportadas| 
--- | --- |
$geoWithin | Sim |
$geoIntersects | Sim | 
$near |  Sim |
$nearSphere |  Sim |
$geometry |  Sim |
$minDistance | Sim |
$maxDistance | Sim |
$center | Sim |
$centerSphere | Sim |
$box | Sim |
$polygon |  Sim |

## <a name="cursor-methods"></a>Métodos de cursor

|Comando  |Suportadas |
|---------|---------|
|cursor.batchSize() |   Sim|
|cursor.close() |Sim|
|cursor.isClosed()|     Sim|
|cursor.collation()|    Não|
|cursor.comment()   |Sim|
|cursor.count() |Sim|
|cursor.explicar()|  Não|
|cursor.forEach()   |Sim|
|cursor.hasNext()   |Sim|
|cursor.hint()  |Sim|
|cursor.isExhausted()|  Sim|
|cursor.itcount()   |Sim|
|cursor.limit() |Sim|
|cursor.map()   |Sim|
|cursor.maxScan()   |Sim|
|cursor.maxTimeMS()|    Sim|
|cursor.max()   |Sim|
|cursor.min()   |Sim|
|cursor.next()| Sim|
|cursor.noCursorTimeout()   |Não|
|cursor.objsLeftInBatch()   |Sim|
|cursor.pretty()|   Sim|
|cursor.readPreocupação()|  Sim|
|cursor.readPref()      |Sim|
|cursor.returnKey() |Não|
|cursor.showRecordId()| Não|
|cursor.size()  |Nes|
|cursor.skip()  |Sim|
|cursor.sort()  |   Sim|
|cursor.tailable()| Não|
|cursor.toArray()   |Sim|

## <a name="sort-operations"></a>Ordenar operações

Ao utilizar a operação `findOneAndUpdate`, são apoiadas operações de triagem num único campo, mas as operações de triagem em vários campos não são suportadas.

## <a name="unique-indexes"></a>Índices exclusivos

Os índices exclusivos garantem que um campo específico não tem valores duplicados em todos os documentos numa coleção, semelhante ao modo de preservação da exclusividade na chave "_id" predefinida. Você pode criar índices personalizados no Cosmos DB usando o comando createIndex, incluindo a restrição ' Unique '.

## <a name="time-to-live-ttl"></a>TTL

O Cosmos DB dá suporte a TTL (vida útil) com base no carimbo de data/hora do documento. A TTL pode ser ativada para recolhas indo para o [portal Azure.](https://portal.azure.com)

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

Cosmos DB ainda não dá suporte a usuários e funções. No entanto, cosmos DB suporta o controlo de acesso baseado em papéis (RBAC) e leia e leia senhas/teclas que podem ser obtidas através do [portal Azure](https://portal.azure.com) (página De conexão String).

## <a name="replication"></a>Replicação

O Cosmos DB dá suporte à replicação nativa e automática nas camadas mais baixas. Esta lógica é expandida para conseguir também a replicação global de latência baixa. Cosmos DB não oferece suporte a comandos de replicação manual.

## <a name="write-concern"></a>Preocupação Escrita

Algumas aplicações baseiam-se numa [Preocupação](https://docs.mongodb.com/manual/reference/write-concern/) de Escrita que especifica o número de respostas necessárias durante uma operação de escrita. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as escritas são automaticamente Quórum por predefinição. Qualquer preocupação de gravação especificada pelo código do cliente é ignorada. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho).

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB suporta a fragmentação automática do lado do servidor. Ele gerencia a criação, o posicionamento e o balanceamento de fragmentos automaticamente. Azure Cosmos DB não dá suporte a comandos de fragmentação manual, o que significa que você não precisa invocar comandos como addfragment, balancerStart, moveChunk, etc. Você só precisa especificar a chave de fragmentação ao criar os contêineres ou consultar os dados.

## <a name="sessions"></a>Sessões

O Azure Cosmos DB ainda não dá suporte a comandos de sessões do lado do servidor.

## <a name="next-steps"></a>Passos Seguintes

- Para mais informações consulte as [funcionalidades da versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Aprenda a [usar robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.

<sup>Nota: Este artigo descreve uma característica do Azure Cosmos DB que proporciona compatibilidade de protocolo de arame com bases de dados mongoDB. A Microsoft não executa bases de dados Do MongoDB para fornecer este serviço. A Azure Cosmos DB não está afiliada à MongoDB, Inc.</sup>
