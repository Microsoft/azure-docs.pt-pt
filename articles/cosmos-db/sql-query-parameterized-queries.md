---
title: Consultas parametrizadas em Azure Cosmos DB
description: Saiba como as consultas parametrizadas SQL proporcionam um manuseamento robusto e fuga da entrada do utilizador e evite a exposição acidental de dados através da injeção de SQL.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549162"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB suporta consultas com parâmetros expressos pela notação familiar @ O SQL parametrizado proporciona um manuseamento robusto e fuga da entrada do utilizador, e evita a exposição acidental de dados através da injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, pode escrever uma consulta que toma `lastName` e `address.state` como parâmetros, e executá-la por vários valores e com base na entrada do `lastName` `address.state` utilizador.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, pode enviar este pedido para Azure Cosmos DB como uma consulta JSON parametrizada como o seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento TOP com uma consulta parametrizada:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Os valores dos parâmetros podem ser qualquer JSON válido: cordas, números, Booleans, nulas, até mesmo matrizes ou JSON aninhado. Uma vez que a Azure Cosmos DB é sem esquemas, os parâmetros não são validados contra qualquer tipo.

Aqui estão exemplos para consultas parametrizadas em cada Azure Cosmos DB SDK:

- [SDK do .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Passos seguintes

- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
