---
title: Trabalhar com a JSON em Azure Cosmos DB
description: Aprenda sobre a consulta e aceda às propriedades da JSON aninhada e use personagens especiais em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: a8f32ad69d32844305c1cc785afc9f1df3c102b8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006355"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Trabalhar com a JSON em Azure Cosmos DB

Na API SQL (Core) da Azure Cosmos DB, os itens são armazenados como JSON. O sistema de tipo e expressões são restritos a lidar apenas com tipos JSON. Para mais informações, consulte a [especificação JSON](https://www.json.org/).

Vamos resumir alguns aspetos importantes de trabalhar com a JSON:

- Os objetos JSON `{` começam sempre com `}` uma cinta esquerda e terminam com uma cinta direita
- Você pode ter propriedades JSON [aninhadas](#nested-properties) dentro umas das outras
- Os valores de propriedade da JSON podem ser matrizes
- Os nomes de propriedade da JSON são sensíveis ao caso
- O nome da propriedade JSON pode ser qualquer valor de string (incluindo espaços ou caracteres que não são letras)

## <a name="nested-properties"></a>Propriedades aninhadas

Pode aceder à JSON aninhada utilizando um acessório de pontos. Você pode usar propriedades JSON aninhadas nas suas consultas da mesma forma que você pode usar quaisquer outras propriedades.

Aqui está um documento com a aninhada JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Neste caso, `state`as `country`propriedades `city` e propriedades estão `address` todas aninhadas dentro da propriedade.

O exemplo seguinte projeta duas `f.address.state` `f.address.city`propriedades aninhadas: e .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Trabalhar com matrizes

Além das propriedades aninhadas, a JSON também suporta matrizes.

Aqui está um documento de exemplo com uma matriz:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Ao trabalhar com matrizes, pode aceder a um elemento específico dentro da matriz, referindo a sua posição:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Na maioria dos casos, no entanto, você usará uma [subqueria](sql-query-subquery.md) ou [auto-juntar-se-á](sql-query-join.md) ao trabalhar com matrizes.

Por exemplo, aqui está um documento que mostra o saldo diário da conta bancária de um cliente.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Se quisesse fazer uma consulta que mostrasse a todos os clientes que tinham um saldo negativo em algum momento, poderia usar [existem](sql-query-subquery.md#exists-expression) com uma subqueria:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>Palavras-chave reservadas e caracteres especiais em JSON

Pode aceder a propriedades utilizando `[]`o operador de propriedade citado. Por `SELECT c.grade` exemplo, `SELECT c["grade"]` e são equivalentes. Esta sintaxe é útil para escapar de uma propriedade que contém espaços, caracteres especiais, ou tem o mesmo nome que uma palavra-chave SQL ou palavra reservada.

Por exemplo, aqui está um documento `order` com `price($)` uma propriedade nomeada e uma propriedade que contém caracteres especiais:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se executar uma consulta que `order` inclua a propriedade ou `price($)` propriedade, receberá um erro de sintaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

O resultado é:

`
Syntax error, incorrect syntax near 'order'
`

Deve reescrever as mesmas consultas que as seguintes:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Expressões JSON

A projeção também apoia as expressões JSON, como mostra o seguinte exemplo:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

No exemplo anterior, `SELECT` a cláusula precisa de criar um objeto JSON, e uma vez que `$1`a amostra não fornece nenhuma chave, a cláusula utiliza o nome variável de argumento implícito . A seguinte consulta devolve duas variáveis implícitas de argumentos: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Aliasing

Pode explicitamente alias valores em consultas. Se uma consulta tiver duas propriedades com o mesmo nome, use o pseudónimo para mudar o nome de uma ou ambas as propriedades para que sejam desambiguadas no resultado projetado.

### <a name="examples"></a>Exemplos

A `AS` palavra-chave utilizada para aliasing é opcional, como mostra o `NameInfo`seguinte exemplo ao projetar o segundo valor como:

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing com palavras-chave reservadas ou caracteres especiais

Não pode usar o pseudónimo para projetar um valor como nome de propriedade com um espaço, caráter especial ou palavra reservada. Se quisesse alterar a projeção de um valor para, por exemplo, ter um nome de propriedade com um espaço, poderia usar uma [expressão JSON](#json-expressions).

Segue-se um exemplo:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           { "JSON expression with a special character": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [ONDE a cláusula](sql-query-where.md)
