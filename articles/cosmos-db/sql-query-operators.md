---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Conheça os operadores SQL, tais como igualdade, comparação e operadores lógicos apoiados pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063561"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores em Azure Cosmos DB

Este artigo detalha os vários operadores apoiados pela Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de Igualdade e Comparação

O quadro seguinte mostra o resultado de comparações de igualdade no API SQL entre dois tipos de JSON.

| **Op** | **Indefinido** | **Null** | **Booleano** | **Number** | **String** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Null** | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Booleano** | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Number** | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido |
| **String** | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** |

Para operadores de comparação `>` `>=` como, `!=` , e `<` , `<=` comparação entre tipos ou entre dois objetos ou matrizes produz `Undefined` .  

Se o resultado da expressão escalar `Undefined` for, o item não está incluído no resultado, porque `Undefined` não é igual `true` .

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (E, OR e NÃO)

Os operadores lógicos operam com valores booleanas. As tabelas que se seguem mostram as tabelas lógicas de verdade para estes operadores:

**Operador de OR**

Devoluções `true` quando qualquer uma das condições estiver `true` .

|  | **Verdade** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **Verdade** |Verdadeiro |Verdadeiro |Verdadeiro |
| **Falso** |Verdadeiro |Falso |Indefinido |
| **Indefinido** |Verdadeiro |Indefinido |Indefinido |

**E operador**

Retorna `true` quando ambas as expressões são `true` .

|  | **Verdade** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **Verdade** |Verdadeiro |Falso |Indefinido |
| **Falso** |Falso |Falso |Falso |
| **Indefinido** |Indefinido |Falso |Indefinido |

**NÃO operador**

Inverte o valor de qualquer expressão booleana.

|  | **NÃO** |
| --- | --- |
| **Verdade** |Falso |
| **Falso** |Verdadeiro |
| **Indefinido** |Indefinido |

**Precedência do operador**

Os operadores `OR` lógicos , e têm o `AND` `NOT` nível de precedência indicado abaixo:

| **Operador** | **Priority** |
| --- | --- |
| **NÃO** |1 |
| **E** |2 |
| **OU** |3 |

## <a name="-operator"></a>* operador

O operador especial * projeta todo o item como está. Quando usado, deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válida, mas `SELECT VALUE * FROM Families f` não é `SELECT *, f.id FROM Families f` válida.

## <a name="-and--operators"></a>? e ?? operadores

Pode utilizar os operadores Ternary (?) e Coalesce (??) para construir expressões condicionais, como em linguagens de programação como C# e JavaScript.

Pode utilizar o ? operador para construir novas propriedades JSON em movimento. Por exemplo, a seguinte consulta classifica os níveis de nota `elementary` em: `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Também pode fazer chamadas para o ninho? Operador, tal como na seguinte consulta: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Como com outros operadores de consultas, o? O operador exclui os itens se as propriedades referenciadas estiverem em falta ou os tipos que estão a ser comparados forem diferentes.

Usar o ?? operador para verificar eficazmente se há uma propriedade num item quando consultar dados semi-estruturados ou de tipo misto. Por exemplo, a seguinte consulta `lastName` retorna se presente, ou `surname` se não estiver `lastName` presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passos seguintes

- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
