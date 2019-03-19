---
title: Utilizar a API do Azure Cosmos DB para suporte de funcionalidades do MongoDB
description: Saiba mais sobre o suporte de funcionalidades que fornece de API do Azure Cosmos DB para o MongoDB para o MongoDB 3.4.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 795002d450505b9103f1815a8b03c8d546bddde8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119467"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-supported-features-and-syntax"></a>API do Azure Cosmos DB para o MongoDB: suporte de funcionalidades e sintaxe

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API do Azure Cosmos DB para o MongoDB a utilizar o cliente do MongoDB de código-fonte aberto [drivers](https://docs.mongodb.org/ecosystem/drivers). API do Azure Cosmos DB do MongoDB permite a utilização de controladores existentes do cliente através do respetivo do MongoDB [conectar protocolo](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Ao utilizar a API do Azure Cosmos DB para o MongoDB, pode desfrutar dos benefícios do MongoDB está habituado a, com todas as capacidades de empresa que fornece o Cosmos DB: [distribuição global](distribute-data-globally.md), [fragmentação automática](partition-data.md), disponibilidade e a latência das garantias, automáticas de indexação de cada campo, a encriptação em repouso, as cópias de segurança e muito mais.

## <a name="protocol-support"></a>Suporte de protocolo

API do Azure Cosmos DB para o MongoDB é compatível com a versão de servidor do MongoDB **3.2** por predefinição. Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. As funcionalidades ou operadores de consulta adicionados na versão **3.4** do MongoDB estão atualmente disponíveis como uma funcionalidade de pré-visualização. Qualquer controlador de cliente que entende esses protocolos deve ser capaz de ligar à API do Azure Cosmos DB para MongoDB.

O [pipeline de agregação do MongoDB](#aggregation-pipeline) também está atualmente disponível como uma funcionalidade de pré-visualização separada.

## <a name="query-language-support"></a>Suporte de linguagem de consulta

API do Azure Cosmos DB para o MongoDB fornece suporte abrangente para construções de linguagem de consulta do MongoDB. Abaixo encontra a lista detalhada de operações, operadores, fases, comandos e opções atualmente suportados.

## <a name="database-commands"></a>Comandos da base de dados

API do Azure Cosmos DB para o MongoDB suporta os seguintes comandos de base de dados:

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e de escrita
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Comandos de autenticação
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandos de administração
- dropDatabase
- listCollections
- drop
- criar
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandos de diagnóstico
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

O cosmos DB suporta pipeline de agregação em pré-visualização pública. Consulte o [blogue do Azure](https://aka.ms/mongodb-aggregation) para receber instruções sobre como integrar a pré-visualização pública.

### <a name="aggregation-commands"></a>Comandos de agregação
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fases de agregação
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Expressões de agregação

#### <a name="boolean-expressions"></a>Expressões booleanas
- $and
- $or
- $not

#### <a name="set-expressions"></a>Expressões de definição
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Expressões de comparação
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Expressões aritméticas
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Expressões de cadeia
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Expressões de matriz
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Expressões de data
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Expressões condicionais
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Acumuladores de agregação
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operadores

Os seguintes operadores são suportados com exemplos correspondentes da respetiva utilização. Considere este documento de exemplo utilizado nas consultas abaixo:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operador | Exemplo |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |
$lte | ``` { "Elevation": { $lte: 5000 } } ``` |
$ne | ``` { "Elevation": { $ne: 1 } } ``` |
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` |
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |
$exists | ``` { "Status": { $exists: true } } ```|
$type | ``` { "Status": { $type: "string" } } ```|
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|

### <a name="notes"></a>Notas

Nas consultas $regex, as expressões ancoradas à esquerda permitem uma pesquisa de índice. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.
Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex. Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, tem de ser modificada como se segue: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas. O operador barra "|" atua como uma função "or" – a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operadores de atualização de matriz
- $addToSet
- $pop
- $pullAll
- $pull (nota: $pull com condição não é suportado)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit
- $bit

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Exemplo | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sim |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sim |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sim |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sim |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sim |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sim |

## <a name="sort-operations"></a>Operações de ordenação
Ao utilizar o `findOneAndUpdate` operação, operações de ordenação num único campo são suportadas mas operações de ordenação em múltiplos campos não são suportadas.

## <a name="additional-operators"></a>Operadores adicionais

Operador | Exemplo | Notas 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Não suportado. Em alternativa, utilize $regex.

## <a name="unsupported-operators"></a>Operadores não suportados

Os operadores ```$where``` e ```$eval``` não são suportados pelo Azure Cosmos DB.

### <a name="methods"></a>Métodos

São suportados os seguintes métodos:

#### <a name="cursor-methods"></a>Métodos de cursor

Método | Exemplo | Notas 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Os documentos sem chave de ordenação não são devolvidos

## <a name="unique-indexes"></a>Índices exclusivos

O cosmos DB indexa todos os campos nos documentos que são escritos na base de dados por predefinição. Os índices exclusivos garantem que um campo específico não tem valores duplicados em todos os documentos numa coleção, semelhante ao modo de preservação da exclusividade na chave "_id" predefinida. Pode criar índices personalizados no Cosmos DB com o comando createIndex, incluindo a restrição "exclusiva".

Índices exclusivos estão disponíveis para todas as contas do Cosmos com a API do Azure Cosmos DB do MongoDB.

## <a name="time-to-live-ttl"></a>TTL

Suporta a cosmos DB que um time-to-live (TTL) com base no carimbo de hora do documento. Valor de TTL pode ser ativada para coleções ao aceder a [portal do Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

O cosmos DB ainda não suporta utilizadores e funções. No entanto, o Cosmos DB suporta o controlo de acesso baseado em funções (RBAC) e leitura / escrita e só de leitura palavras-passe/chaves que podem ser obtidas através da [portal do Azure](https://portal.azure.com) (página de cadeia de ligação).

## <a name="replication"></a>Replicação

O cosmos DB suporta a replicação automática, nativa em camadas mais baixas. Esta lógica é expandida para conseguir também a replicação global de latência baixa. O cosmos DB não suporta comandos de replicação manual.

## <a name="write-concern"></a>Preocupação Escrita

Alguns aplicativos dependem de um [escrever preocupação](https://docs.mongodb.com/manual/reference/write-concern/) que especifica o número de respostas necessário durante uma operação de escrita. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as escritas são automaticamente Quórum por predefinição. Qualquer escrever preocupação especificada pelo código do cliente é ignorada. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho).

## <a name="sharding"></a>Fragmentação

O cosmos DB suporta fragmentação automática, o lado do servidor. O cosmos DB não suporta comandos de fragmentação manual.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.

<sup>Nota: Este artigo descreve um recurso do Azure Cosmos DB, que fornece a compatibilidade de protocolo de transmissão com bancos de dados do MongoDB. Microsoft não é executado para fornecer este serviço de bases de dados MongoDB. O Azure Cosmos DB não está afiliado a MongoDB, Inc.</sup>
