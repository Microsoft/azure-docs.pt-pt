---
title: Expressões escalar em consultas Azure Cosmos DB SQL
description: Conheça a expressão escalar SQL sintaxe para Azure Cosmos DB. Este artigo também descreve como combinar expressões escalar em expressões complexas usando operadores.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870739"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Expressões escalar em consultas Azure Cosmos DB SQL

A [cláusula SELECT](sql-query-select.md) suporta expressões escalar. Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados para obter um único valor. Exemplos de expressões escalar incluem: constantes, referências de propriedade, referências de elementos de matriz, referências de pseudónimos ou chamadas de função. Expressões escalar podem ser combinadas em expressões complexas usando operadores.

## <a name="syntax"></a>Sintaxe
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumentos
  
- `<constant>`  
  
   Representa um valor constante. Consulte a secção [Constants](sql-query-constants.md) para obter mais detalhes.  
  
- `input_alias`  
  
   Representa um valor definido `input_alias` pela introdução `FROM` na cláusula.  
  Este valor é garantido não ser **indefinido** – valores**indefinidos** na entrada são ignorados.  
  
- `<scalar_expression>.property_name`  
  
   Representa um valor da propriedade de um objeto. Se o imóvel não existir ou se a propriedade for referenciada num valor, que não é um objeto, então a expressão avalia para valor **indefinido.**  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa um valor da propriedade `property_name` com nome `array_index` ou elemento de matriz com índice de uma matriz. Se o índice de propriedade/matriz não existir ou se o índice de propriedade/matriz for referenciado num valor que não é um objeto/matriz, então a expressão avalia para valor indefinido.  
  
- `unary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a um único valor. Consulte a secção [Operadoras](sql-query-operators.md) para obter mais detalhes.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a dois valores. Consulte a secção [Operadoras](sql-query-operators.md) para obter mais detalhes.  
  
- `<scalar_function_expression>`  
  
   Representa um valor definido pelo resultado de uma chamada de função.  
  
- `udf_scalar_function`  
  
   Nome da função escalar definida pelo utilizador.  
  
- `builtin_scalar_function`  
  
   Nome da função escalar incorporada.  
  
- `<create_object_expression>`  
  
   Representa um valor obtido através da criação de um novo objeto com propriedades especificadas e seus valores.  
  
- `<create_array_expression>`  
  
   Representa um valor obtido através da criação de uma nova matriz com valores especificados como elementos  
  
- `parameter_name`  
  
   Representa um valor do nome de parâmetro especificado. Os nomes dos \@ parâmetros devem ter um único como primeiro personagem.  
  
## <a name="remarks"></a>Observações
  
  Ao chamar uma função escalar incorporada ou definida pelo utilizador, todos os argumentos devem ser definidos. Se algum dos argumentos não for definido, a função não será chamada e o resultado será indefinido.  
  
  Ao criar um objeto, qualquer propriedade que seja atribuída valor indefinido será ignorada e não incluída no objeto criado.  
  
  Ao criar uma matriz, qualquer valor de elemento que seja atribuído valor **indefinido** será ignorado e não incluído no objeto criado. Isto fará com que o próximo elemento definido tome o seu lugar de modo a que a matriz criada não tenha ignorado os índices.  

## <a name="examples"></a>Exemplos

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Os resultados são:

```json
    [{
      "$1": 1.33333
    }]
```

Na seguinte consulta, o resultado da expressão escalar é um Boolean:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Subqueides](sql-query-subquery.md)