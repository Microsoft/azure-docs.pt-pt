---
title: Como fazer consultas com SQL na Azure Cosmos DB?
description: Saiba como fazer consultas com SQL na Azure Cosmos DB
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 05/21/2019
ms.reviewer: sngun
ms.openlocfilehash: 9c6bd2040cb90f7a837efdf11d33a6e6999bc944
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965492"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutorial: Consultar o Azure Cosmos DB com a API de SQL

A [API do SQL](documentdb-introduction.md) da Azure Cosmos DB suporta a consulta de documentos utilizando o SQL. Este artigo fornece um documento de exemplo e dois exemplos de consultas SQL e os resultados.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar dados com o SQL

## <a name="sample-document"></a>Documento de exemplo

As consultas de SQL neste artigo utilizam o documento de exemplo seguinte.

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
## <a name="where-can-i-run-sql-queries"></a>Onde posso executar consultas SQL?

Pode executar consultas utilizando o Data Explorer no portal do Azure, através de [REST API e SDK](sql-api-sdk-dotnet.md)e até mesmo [Teste de consulta](https://www.documentdb.com/sql/demo), que executada consultas num conjunto de dados de exemplo.

Para obter mais informações sobre as consultas SQL, consulte:
* [Consulta SQL e sintaxe SQL](how-to-sql-query.md)

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que tem uma conta e coleção da Azure Cosmos DB. Não tem qualquer um destes? Conclua o [guia de início rápido de 5 minutos](create-mongodb-nodejs.md).

## <a name="example-query-1"></a>Consulta de exemplo 1

Tendo em conta o documento de família de exemplo acima, a seguinte consulta SQL devolve os documentos em que o campo ID corresponde a `WakefieldFamily`. Uma vez que é uma declaração `SELECT *`, o resultado da consulta é o documento JSON completo:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Resultados**

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

## <a name="example-query-2"></a>Consulta de exemplo 2

A seguinte consulta devolve todos os nomes de elementos subordinados na família cujo ID corresponde aos `WakefieldFamily` ordenados pelo respetivo nível.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Resultados**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a fazer consultas utilizando o SQL  

Agora pode avançar para o tutorial seguinte para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-sql-api.md)

