---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Conheça operadores SQL como igualdade, comparação e operadores lógicos apoiados pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063561"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores em Azure Cosmos DB

Este artigo detalha os vários operadores apoiados pela Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de igualdade e comparação

O quadro seguinte mostra o resultado das comparações de igualdade no SQL API entre qualquer dois tipos JSON.

| **Op** | **Indefinido** | **Nulo** | **Boolean** | **Número** | **String** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Nulo** | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Boolean** | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido |
| **String** | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** |

Para operadores `>`de `>=` `!=`comparação como , e `<` `<=`, comparação `Undefined`entre tipos ou entre dois objetos ou matrizes produzem.  

Se o resultado da expressão `Undefined`escalar for , o item não `Undefined` está incluído `true`no resultado, porque não é igual .

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (E, OU e NÃO)

Os operadores lógicos operam com valores booleanos. As tabelas que se seguem mostram as tabelas lógicas da verdade para estes operadores:

**Operador ou**

Devoluções `true` quando qualquer `true`uma das condições é .

|  | **Verdadeiro** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **Verdadeiro** |Verdadeiro |Verdadeiro |Verdadeiro |
| **Falso** |Verdadeiro |Falso |Indefinido |
| **Indefinido** |Verdadeiro |Indefinido |Indefinido |

**E operador**

Devoluções `true` quando ambas `true`as expressões são .

|  | **Verdadeiro** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **Verdadeiro** |Verdadeiro |Falso |Indefinido |
| **Falso** |Falso |Falso |Falso |
| **Indefinido** |Indefinido |Falso |Indefinido |

**NÃO operador**

Inverte o valor de qualquer expressão booleana.

|  | **NÃO.** |
| --- | --- |
| **Verdadeiro** |Falso |
| **Falso** |Verdadeiro |
| **Indefinido** |Indefinido |

**Precedência do operador**

Os operadores lógicos, `OR` `AND`e `NOT` têm o nível de precedência abaixo indicado:

| **Operador** | **Prioridade** |
| --- | --- |
| **NÃO.** |1 |
| **E E** |2 |
| **OU** |3 |

## <a name="-operator"></a>* operador

O operador especial * projeta todo o item como está. Quando utilizado, deve ser o único campo projetado. Uma consulta `SELECT * FROM Families f` como é `SELECT VALUE * FROM Families f` válida, mas não `SELECT *, f.id FROM Families f` é válida.

## <a name="-and--operators"></a>? e ?? operadores

Você pode usar os operadores Ternary (?) e Coalesce (??) para construir expressões condicionais, como em linguagens de programação como C# e JavaScript.

Pode utilizar o ? operador para construir novas propriedades JSON em movimento. Por exemplo, a seguinte consulta classifica os `elementary` `other`níveis de nota em ou:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Também pode fazer ninhos de chamadas para o? operador, como na seguinte consulta: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Como com outros operadores de consulta, o? operador exclui itens se faltarem as propriedades referenciadas ou se os tipos que estão a ser comparados forem diferentes.

Usar o ?? operador verificar eficazmente um imóvel num item quando se consulta com dados semi-estruturados ou mistos. Por exemplo, a seguinte `lastName` consulta retorna se presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passos seguintes

- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
