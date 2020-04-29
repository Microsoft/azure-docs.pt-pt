---
title: Cláusula SELECT em Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para O Azure Cosmos DB. Use o SQL como uma linguagem de consulta Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469940"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT em Azure Cosmos DB

Todas as consultas consistem numa cláusula SELECT e cláusulas opcionais [FROM](sql-query-from.md) e [WHERE,](sql-query-where.md) de acordo com as normas ANSI SQL. Tipicamente, a fonte na cláusula FROM é enumerada, e a cláusula WHERE aplica um filtro na fonte para recuperar um subconjunto de itens JSON. A cláusula SELECT projeta então os valores JSON solicitados na lista selecionada.

## <a name="syntax"></a>Sintaxe

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<select_specification>`  

  Propriedades ou valor a selecionar para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem fazer alterações. Especificamente se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a recuperar. Cada valor devolvido será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isto, `<property_list>` ao contrário de não embrulhar o valor projetado num objeto.  
 
- `DISTINCT`
  
  Especifica que os duplicados de propriedades projetadas devem ser removidos.  

- `<scalar_expression>`  

  Expressão que representa o valor a calcular. Consulte a secção [de expressões Scalar](sql-query-scalar-expressions.md) para obter mais detalhes.  

## <a name="remarks"></a>Observações

A `SELECT *` sintaxe só é válida se a cláusula FROM tiver declarado exatamente um pseudónimo. `SELECT *`fornece uma projeção de identidade, que pode ser útil se não for necessária nenhuma projeção. SELECT * só é válido se a cláusula FROM for especificada e introduzida apenas uma única fonte de entrada.  
  
Ambos `SELECT <select_list>` `SELECT *` são "açúcar sintático" e podem ser expressos alternativamente usando simples declarações SELECT como mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta `address` `Families` SELECT `id` `AndersenFamily`seguinte retorna de cujos fósforos:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Acessório de propriedade cotado
Você pode aceder a propriedades usando o operador de propriedade citado []. Por `SELECT c.grade` exemplo, `SELECT c["grade"]` e são equivalentes. Esta sintaxe é útil para escapar de uma propriedade que contém espaços, caracteres especiais, ou tem o mesmo nome que uma palavra-chave SQL ou palavra reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Propriedades aninhadas

O exemplo seguinte projeta duas `f.address.state` `f.address.city`propriedades aninhadas, e .

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
### <a name="json-expressions"></a>Expressões JSON

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

No exemplo anterior, a cláusula SELECT precisa de criar um objeto JSON, e uma vez que `$1`a amostra não fornece nenhuma chave, a cláusula utiliza o nome variável de argumento implícito . A seguinte consulta devolve duas variáveis implícitas de argumentos: `$1` e `$2`.

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
## <a name="reserved-keywords-and-special-characters"></a>Palavras-chave reservadas e caracteres especiais

Se os seus dados contenham propriedades com os mesmos nomes que as palavras-chave reservadas, tais como "encomenda" ou "Grupo", então as consultas contra estes documentos resultarão em erros de sintaxe. Você deve explicitamente `[]` incluir a propriedade em personagem para executar a consulta com sucesso.

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ONDE a cláusula](sql-query-where.md)
