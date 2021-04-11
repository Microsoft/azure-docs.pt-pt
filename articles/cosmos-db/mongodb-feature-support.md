---
title: A API da Azure Cosmos DB para a versão MongoDB (versão 3.2) suportada e sintaxe
description: Saiba mais sobre a API suportada pela Azure Cosmos DB para a versão MongoDB (versão 3.2).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ce6b01a99c29020b0ede5a860da9d3d6f37e9f06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605754"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>API do Azure Cosmos DB para MongoDB (versão 3.2): sintaxe e funcionalidades com suporte
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode comunicar com a API da Azure Cosmos DB para a MongoDB usando qualquer um dos [motoristas](https://docs.mongodb.org/ecosystem/drivers)de clientes de código aberto MongoDB . A API do Azure Cosmos DB para MongoDB permite a utilização dos controladores cliente existentes através do [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Ao utilizar a API da Azure Cosmos para a MongoDB, poderá usufruir dos benefícios do MongoDB a que está habituado, com todas as capacidades empresariais que a Cosmos DB fornece: [distribuição global, fragmentos](distribute-data-globally.md) [automáticos,](partitioning-overview.md)disponibilidade e garantias de latência, indexação automática de todos os campos, encriptação em repouso, backups e muito mais.

> [!NOTE]
> A versão 3.2 da API cosmos DB para a MongoDB não tem planos atuais para o fim da vida (EOL). O aviso mínimo para um futuro EOL é de três anos.

## <a name="protocol-support"></a>Apoio ao Protocolo

Todas as novas contas da API da Azure Cosmos DB para a MongoDB são compatíveis com a versão **3.6** do servidor MongoDB . Este artigo aborda a versão 3.2 do MongoDB. Os operadores suportados e quaisquer limitações ou exceções são listadas abaixo. Qualquer controlador do cliente que compreenda estes protocolos deverá conseguir ligar à API do Azure Cosmos DB para MongoDB. 

A API da Azure Cosmos DB para a MongoDB também oferece uma experiência de upgrade perfeita para contas elegíveis. Saiba mais sobre o guia de atualização da [versão MongoDB](mongodb-version-upgrade.md).

## <a name="query-language-support"></a>Suporte linguístico de consulta

A API da Azure Cosmos DB para o MongoDB fornece um suporte abrangente para construções linguísticas de consulta mongoDB. Abaixo pode encontrar a lista detalhada de operações apoiadas atualmente, operadores, etapas, comandos e opções.

## <a name="database-commands"></a>Comandos da base de dados

A API da Azure Cosmos DB para a MongoDB suporta os seguintes comandos de base de dados:

> [!NOTE]
> Este artigo apenas lista os comandos do servidor suportado e exclui as funções de invólucro do lado do cliente. Funções de invólucro do lado do cliente, tais como `deleteMany()` e `updateMany()` utilizar internamente os `delete()` comandos e `update()` servidor. As funções que utilizam comandos de servidor suportados são compatíveis com a API da Azure Cosmos DB para o MongoDB.

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

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

Cosmos DB suporta o gasoduto de agregação para MongoDB 3.2 em pré-visualização pública. Consulte o [blogue do Azure](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/) para receber instruções sobre como integrar a pré-visualização pública.

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

| Operador | Exemplo |
| --- | --- |
| $eq | `{ "Volcano Name": { $eq: "Rainier" } }` |
| $gt | `{ "Elevation": { $gt: 4000 } }` | 
| $gte | `{ "Elevation": { $gte: 4392 } }` | 
| $lt | `{ "Elevation": { $lt: 5000 } }` | 
| $lte | `{ "Elevation": { $lte: 5000 } }` | 
| $ne | `{ "Elevation": { $ne: 1 } }` | 
| $in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |
| $nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` |
| $or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` | 
| $and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |
| $not | `{ "Elevation": { $not: { $gt: 5000 } } }`| 
| $nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |
| $exists | `{ "Status": { $exists: true } }`|
| $type | `{ "Status": { $type: "string" } }`| 
| $mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` | 
| $regex | `{ "Volcano Name": { $regex: "^Rain"} }`| 

### <a name="notes"></a>Notas

Nas consultas $regex, as expressões ancoradas à esquerda permitem uma pesquisa de índice. No entanto, utilizar o modificador "i" (não sensível a maiúsculas e minúsculas) e o modificador "m" (multinha) faz a análise de coleção em todas as expressões.
Quando for necessário incluir "$" ou "|", é melhor criar duas (ou mais) consultas de regex.
Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, tem de ser modificada como se segue: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
A primeira parte irá utilizar o índice para restringir a pesquisa aos documentos que começam por ^abc e a segunda parte vai fazer a correspondência com as entradas exatas.
O operador barra "|" atua como uma função "or" – a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos cujo campo "x" tem valores que começam por "abc" ou "def". Para utilizar o índice, é recomendado dividir a consulta em duas consultas diferentes associadas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

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

Operador | Exemplo | Suportado |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Yes |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Yes |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Yes |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Yes |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Yes |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Yes |

## <a name="sort-operations"></a>Ordenar Operações

Ao utilizar a `findOneAndUpdate` operação, as operações de classificação num único campo são suportadas, mas as operações de classificação em vários campos não são suportadas.

## <a name="other-operators"></a>Outros operadores

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

Cosmos DB indexa todos os campos em documentos que são escritos na base de dados por padrão. Índices únicos garantem que um campo específico não tem valores duplicados em todos os documentos de uma coleção, semelhante à forma como a singularidade é preservada na `_id` chave padrão. Pode criar índices personalizados em Cosmos DB utilizando o comando createIndex, incluindo a restrição 'única'.

Estão disponíveis índices exclusivos para todas as contas cosmos utilizando a API da Azure Cosmos DB para o MongoDB.

## <a name="time-to-live-ttl"></a>TTL

Cosmos DB suporta um time-to-live (TTL) baseado no tempotamp do documento. A TTL pode ser ativada para coleções indo ao [portal Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestão de funções e utilizadores

A Cosmos DB ainda não suporta utilizadores e funções. No entanto, a Cosmos DB suporta o controlo de acesso baseado em funções (Azure RBAC) e palavras-passe/chaves só de leitura e leitura que podem ser obtidas através do [portal Azure](https://portal.azure.com) (página de cadeia de ligação).

## <a name="replication"></a>Replicação

Cosmos DB suporta replicação automática e nativa nas camadas mais baixas. Esta lógica é expandida para conseguir também a replicação global de latência baixa. Cosmos DB não suporta comandos de replicação manual.

## <a name="write-concern"></a>Preocupação Escrita

Algumas aplicações baseiam-se numa [Comissão de Escrita](https://docs.mongodb.com/manual/reference/write-concern/) que especifica o número de respostas necessárias durante uma operação de escrita. Devido à forma como o Cosmos DB lida com a replicação em segundo plano, todas as escritas são automaticamente Quórum por predefinição. Qualquer preocupação de escrita especificada pelo código do cliente é ignorada. Saiba mais em [Using consistency levels to maximize availability and performance](consistency-levels.md) (Utilizar níveis de consistência para maximizar a disponibilidade e desempenho).

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB suporta a fragmentação automática do lado do servidor. Gere a criação, colocação e equilíbrio de fragmentos automaticamente. A Azure Cosmos DB não suporta comandos manuais de sharding, o que significa que não tem de invocar comandos como shardCollection, addShard, balancerStart, moveChunk etc. Basta especificar a chave de fragmentos enquanto cria os recipientes ou consulta os dados.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
