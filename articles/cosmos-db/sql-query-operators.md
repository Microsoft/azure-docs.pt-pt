---
title: Operadores de consulta SQL para o Azure Cosmos DB
description: Saiba mais sobre os operadores SQL para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342651"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores no Azure Cosmos DB

Este artigo fornece detalhes sobre os vários operadores suportados pelo Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Igualdade e operadores de comparação

A tabela seguinte mostra o resultado de comparações de igualdade na API do SQL entre quaisquer dois tipos JSON.

| **OP** | **Não definido** | **Nulo** | **valor booleano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Não definido** | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido |
| **Nulo** | Não definido | **OK** | Não definido | Não definido | Não definido | Não definido | Não definido |
| **valor booleano** | Não definido | Não definido | **OK** | Não definido | Não definido | Não definido | Não definido |
| **Número** | Não definido | Não definido | Não definido | **OK** | Não definido | Não definido | Não definido |
| **Cadeia de caracteres** | Não definido | Não definido | Não definido | Não definido | **OK** | Não definido | Não definido |
| **Objeto** | Não definido | Não definido | Não definido | Não definido | Não definido | **OK** | Não definido |
| **Matriz** | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido | **OK** |

Para os operadores de comparação, como `>`, `>=`, `!=`, `<`, e `<=`, comparação em tipos de ou entre dois objetos ou matrizes produz `Undefined`.  

Se for o resultado da expressão escalar `Undefined`, o item não está incluído no resultado, porque `Undefined` não é igual a `true`.

## <a name="logical-and-or-and-not-operators"></a>Lógicos (AND, OR e não) operadores

Operadores lógicos operam em valores booleanos. As tabelas seguintes mostram as tabelas de verdade lógicas para esses operadores:

**OU o operador**

| OU | Verdadeiro | Falso | Não definido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Verdadeiro |Verdadeiro |
| Falso |Verdadeiro |Falso |Não definido |
| Não definido |Verdadeiro |Não definido |Não definido |

**E o operador**

| E | Verdadeiro | Falso | Não definido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Falso |Não definido |
| Falso |Falso |Falso |Falso |
| Não definido |Não definido |Falso |Não definido |

**Operador NOT**

| NÃO |  |
| --- | --- |
| Verdadeiro |Falso |
| Falso |Verdadeiro |
| Não definido |Não definido |


## <a name="-operator"></a>* operador

O operador especial * projetos todo o item como está. Quando utilizado, tem de ser o único campo previsto. Uma consulta, como `SELECT * FROM Families f` for válido, mas `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos.

## <a name="-and--operators"></a>? e?? Operadores

Pode utilizar o ternária (?) e Coalesce operadores (?) para criar expressões condicionais, como em linguagens como o C# e JavaScript. 

Pode utilizar o? operador de construir novas propriedades JSON em tempo real. Por exemplo, a seguinte consulta classifica os níveis de nível para o `elementary` ou `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Também pode aninhar chamadas para o? operador, tal como a seguinte consulta: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Tal como acontece com outros operadores de consulta, o? operador exclui itens, se as propriedades referenciadas estão em falta ou os tipos que está a ser comparados são diferentes.

Utilize o?? operador para procurar com eficiência uma propriedade num item ao consultar os dados semiestruturados ou tipo misto. Por exemplo, a consulta seguinte devolve `lastName` se estiver presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Passos Seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
