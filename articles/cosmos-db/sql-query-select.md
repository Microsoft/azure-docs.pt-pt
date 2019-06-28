---
title: Cláusula SELECT na Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para o Azure Cosmos DB. Utilize o SQL como uma linguagem de consulta JSON do Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342601"
---
# <a name="select-clause"></a>Cláusula SELECT

É composta por todas as consultas de uma cláusula SELECT e opcionais [FROM](sql-query-from.md) e [onde](sql-query-where.md) cláusulas como, por padrões ANSI SQL. Normalmente, a origem na cláusula FROM é enumerada e a cláusula WHERE aplica-se um filtro na origem para obter um subconjunto de itens JSON. A cláusula SELECT, em seguida, os valores JSON de pedidos na lista de seleção de projetos.

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

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser obtido sem fazer quaisquer alterações. Especificamente, se o valor processado é um objeto, serão possível obter todas as propriedades.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem obtidas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser obtido em vez do objeto JSON completo. Isso, ao contrário `<property_list>` não envolve o valor previsto num objeto.  
 
- `DISTINCT`
  
  Especifica que as duplicatas de propriedades previstas devem ser removidas.  

- `<scalar_expression>`  

  Expressão que representa o valor a ser calculada. Ver [expressões escalares](sql-query-scalar-expressions.md) secção para obter detalhes.  

## <a name="remarks"></a>Observações

O `SELECT *` sintaxe só é válida se a cláusula FROM tem declarado exatamente um alias. `SELECT *` Fornece uma projeção de identidade, o que pode ser útil se não projeção é necessária. SELECIONE * só é válido se for especificado cláusula FROM e introduziu apenas uma única origem de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e pode ser expresso como alternativa ao utilizar as instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

A seguinte, SELECIONE consulta exemplo devolve `address` partir `Families` cujo `id` corresponde ao `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Acessador de propriedade com aspas
Pode acessar propriedades usando a propriedade delimitados por aspas operator []. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para escapar uma propriedade que contém espaços, carateres especiais, ou que tenha o mesmo nome que uma palavra-chave SQL ou uma palavra reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Propriedades aninhadas

O exemplo a seguir duas propriedades aninhadas, de projetos `f.address.state` e `f.address.city`.

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
### <a name="json-expressions"></a>Expressões de JSON

Projeção também oferece suporte a expressões de JSON, conforme mostrado no exemplo a seguir:

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

No exemplo anterior, a cláusula SELECT tem de criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula utiliza o nome da variável implícita argumento `$1`. A seguinte consulta devolve duas variáveis de argumento implícita: `$1` e `$2`.

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula WHERE](sql-query-where.md)