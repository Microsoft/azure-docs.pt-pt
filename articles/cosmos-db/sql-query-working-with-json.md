---
title: Trabalhar com o JSON no Azure Cosmos DB
description: Saiba como consultar e aceder a propriedades aninhadas da JSON e usar personagens especiais em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337834"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Trabalhar com o JSON no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Na API SQL (Core) do Azure Cosmos DB, os itens são armazenados como JSON. O sistema de tipos e as expressões são restringidos para processar apenas os tipos JSON. Para mais informações, consulte a [especificação JSON.](https://www.json.org/)

Vamos resumir alguns aspetos importantes do trabalho com a JSON:

- Os objetos JSON sempre começam com uma `{` cinta esquerda e terminam com uma `}` cinta direita
- Você pode ter propriedades JSON [aninhadas](#nested-properties) dentro umas das outras
- Os valores de propriedade JSON podem ser matrizes
- Os nomes de propriedade json são sensíveis a casos
- O nome da propriedade JSON pode ser qualquer valor de cadeia (incluindo espaços ou caracteres que não são letras)

## <a name="nested-properties"></a>Propriedades aninhadas

Pode aceder a JSON aninhado utilizando um acessório de pontos. Pode utilizar propriedades JSON aninhadas nas suas consultas da mesma forma que pode utilizar quaisquer outras propriedades.

Aqui está um documento com aninhado JSON:

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

Neste caso, os `state` `country` , e propriedades `city` estão todos aninhados dentro da `address` propriedade.

O exemplo a seguir projeta duas propriedades aninhadas: `f.address.state` e `f.address.city` .

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

Além de propriedades aninhadas, jSON também suporta matrizes.

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

Na maioria dos casos, no entanto, você usará um [sublote ou](sql-query-subquery.md) [auto-junção](sql-query-join.md) quando trabalhar com matrizes.

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

Se quiser fazer uma consulta que mostrasse todos os clientes que tinham um saldo negativo em algum momento, poderia usar [a EXISTÊNCIA](sql-query-subquery.md#exists-expression) com uma subquery:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Diferença entre nulo e indefinido

Se uma propriedade não for definida num item, então o seu valor é `undefined` . Um imóvel com o valor `null` deve ser explicitamente definido e atribuído um `null` valor.

Por exemplo, considere este item da amostra:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

Neste exemplo, o imóvel `isRegistered` tem um valor de porque é `undefined` omitido do item. A propriedade `creationDate` tem um `null` valor.

A Azure Cosmos DB suporta duas funções úteis do sistema de verificação de tipo para `null` e `undefined` propriedades:

* [IS_NULL](sql-query-is-null.md) - verifica se um valor de propriedade é `null`
* [IS_DEFINED](sql-query-is-defined.md) - verifica se um valor de propriedade é definido

Você pode aprender sobre [operadores apoiados](sql-query-operators.md) e seu comportamento para `null` valores e `undefined` valores.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Palavras-chave reservadas e caracteres especiais em JSON

Pode aceder a propriedades utilizando o operador de propriedade `[]` citado. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Esta sintaxe é útil para escapar de uma propriedade que contenha espaços, caracteres especiais, ou tenha o mesmo nome que uma palavra-chave SQL ou palavra reservada.

Por exemplo, aqui está um documento com uma propriedade nomeada `order` e uma propriedade que contém caracteres `price($)` especiais:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se executar uma consulta que inclua a `order` propriedade ou `price($)` propriedade, receberá um erro de sintaxe.

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

Deve reescrever as mesmas consultas que abaixo:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Expressões JSON

A projeção também suporta expressões JSON, como mostra o seguinte exemplo:

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

No exemplo anterior, a `SELECT` cláusula precisa de criar um objeto JSON, e uma vez que a amostra não fornece nenhuma chave, a cláusula utiliza o nome variável de argumento implícito `$1` . A seguinte consulta devolve duas variáveis implícitas de argumento: `$1` e `$2` .

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

Pode explicitamente alias valores em consultas. Se uma consulta tiver duas propriedades com o mesmo nome, use o pseudónimo para renomear uma ou ambas as propriedades para que sejam desambiguadas no resultado projetado.

### <a name="examples"></a>Exemplos

A `AS` palavra-chave utilizada para o aliasing é opcional, como mostra o exemplo a seguir ao projetar o segundo valor como `NameInfo` :

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

Não pode usar o pseudónimo para projetar um valor como nome de propriedade com um espaço, caráter especial ou palavra reservada. Se quiser alterar a projeção de um valor para, por exemplo, ter um nome de propriedade com um espaço, pode utilizar uma [expressão JSON.](#json-expressions)

Eis um exemplo:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)
