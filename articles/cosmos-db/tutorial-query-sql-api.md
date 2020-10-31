---
title: 'Tutorial: Como consultar o SQL em Azure Cosmos DB?'
description: 'Tutorial: Saiba como consultar consultas sql em Azure Cosmos DB usando o parque infantil de consulta'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: af26d0e6c2ada299798aea5b57b704d85874147d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073813"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutorial: consultar a Azure Cosmos DB utilizando a API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A [API do SQL](./introduction.md) da Azure Cosmos DB suporta a consulta de documentos utilizando o SQL. Este artigo fornece um documento de exemplo e dois exemplos de consultas SQL e os resultados.

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
* [Consulta SQL e sintaxe SQL](sql-query-getting-started.md)

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que tem uma conta e coleção da Azure Cosmos DB. Não tem nenhum desses recursos? Conclua o [guia de início rápido de 5 minutos](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Consulta de exemplo 1

Dado o documento familiar da amostra acima, a consulta SQL retorna os documentos onde o campo de identificação corresponde `WakefieldFamily` . Uma vez que é uma declaração `SELECT *`, o resultado da consulta é o documento JSON completo:

**Query**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

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

A próxima consulta devolve todos os nomes de crianças da família cuja identificação corresponde `WakefieldFamily` .

**Query**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Resultados**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez as seguintes tarefas:

> [!div class="checklist"]
> * Aprendeu a fazer consultas utilizando o SQL  

Agora pode avançar para o tutorial seguinte para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-sql-api.md)