---
title: Dados de consulta com a API da Azure Cosmos DB para a MongoDB
description: Saiba como consultar dados da API da Azure Cosmos DB para a MongoDB utilizando comandos de conchas MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074323"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Dados de consulta utilizando a API da Azure Cosmos DB para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A [API da Azure Cosmos DB para a MongoDB](mongodb-introduction.md) apoia [as consultas do MongoDB.](https://docs.mongodb.com/manual/tutorial/query-documents/) 

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Consulta de dados armazenados na sua base de dados Cosmos usando a concha mongoDB

Para começar, pode utilizar os exemplos neste documento e ver o vídeo [Query Azure Cosmos DB with MongoDB shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) (Consultar o Azure Cosmos DB com a shell do MongoDB).

## <a name="sample-document"></a>Documento de exemplo

As consultas neste artigo utilizam o documento de exemplo seguinte.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="example-query-1"></a><a id="examplequery1"></a> Consulta de exemplo 1 

Tendo em conta o documento de família de exemplo acima, a seguinte consulta devolve os documentos em que o campo ID corresponde a `WakefieldFamily`.

**Query**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Resultados**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Consulta de exemplo 2 

A seguinte consulta devolve todos os elementos subordinados na família. 

**Query**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Resultados**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>Consulta de exemplo 3 

A consulta seguinte devolve todas as famílias que estão registadas. 

**Query**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Resultados**

Nenhum documento será devolvido. 

## <a name="example-query-4"></a><a id="examplequery4"></a>Consulta de exemplo 4

A consulta seguinte devolve todas as famílias que não estão registadas. 

**Query**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>Consulta de exemplo 5

A consulta seguinte devolve todas as famílias que não estão registadas e o estado é NY. 

**Query**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>Consulta de exemplo 6

A consulta seguinte devolve todas as famílias em que as classificações de elementos subordinados são 8.

**Query**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Resultados**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>Consulta de exemplo 7

A consulta seguinte devolve todas as famílias em que a matriz de elementos subordinados é 3.

**Query**

```bash
db.Family.find( {children: { $size:3} } )
```

**Resultados**

Não serão devolvidos resultados, porque não há famílias com mais de dois subordinados. Esta consulta só será concluída com êxito e devolve o documento completo se o parâmetro for 2.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendi a consultar a API da Cosmos DB para o MongoDB

Agora pode avançar para o tutorial seguinte para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-sql-api.md)

