---
title: Consultas parametrizadas no Azure Cosmos DB
description: Saiba mais sobre consultas parametrizadas do SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003608"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas no Azure Cosmos DB

Cosmos DB dá suporte a consultas com parâmetros expressos pela conhecida @ Notation. O SQL com parâmetros fornece manipulação e saída robustas de entrada do usuário e impede a exposição acidental de dados por meio de injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, você pode escrever uma consulta que aceite `lastName` e `address.state` como parâmetros e execute-a para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, você pode enviar essa solicitação para Cosmos DB como uma consulta JSON parametrizada como a seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento superior com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Os valores de parâmetro podem ser qualquer JSON: cadeias de caracteres, números, Boolianos, nulos, pares ou JSON aninhados válidos. Como Cosmos DB não tem Esquema, os parâmetros não são validados em relação a nenhum tipo.


## <a name="next-steps"></a>Passos seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento de modelo](modeling-data.md)
