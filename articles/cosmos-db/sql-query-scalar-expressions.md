---
title: Expressões escalares nas consultas de SQL do Azure Cosmos DB
description: Saiba mais sobre expressão escalar a sintaxe SQL do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342838"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Expressões escalares nas consultas de SQL do Azure Cosmos DB

O [cláusula SELECT](sql-query-select.md) oferece suporte a expressões escalares. Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados como para obter um valor único. Exemplos de expressões escalares: constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. Expressões escalares podem ser combinadas em expressões complexas usando operadores.

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
  
   Representa um valor constante. Ver [constantes](sql-query-constants.md) secção para obter detalhes.  
  
- `input_alias`  
  
   Representa um valor definido pela `input_alias` introduzida no `FROM` cláusula.  
  Este valor é garantido que não seja **indefinido** –**indefinido** valores na entrada são ignoradas.  
  
- `<scalar_expression>.property_name`  
  
   Representa um valor da propriedade de um objeto. Se a propriedade não existe ou propriedade é referenciada num valor que não é um objeto, em seguida, a expressão avalia **indefinido** valor.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa um valor da propriedade com o nome `property_name` ou o elemento de matriz com índice `array_index` de uma matriz. Se o índice da matriz/propriedade não existe ou o índice da propriedade/matriz é referenciado num valor que não é uma matriz/objeto, em seguida, a expressão é avaliada como valor indefinido.  
  
- `unary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a um valor único. Ver [operadores](sql-query-operators.md) secção para obter detalhes.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado aos dois valores. Ver [operadores](sql-query-operators.md) secção para obter detalhes.  
  
- `<scalar_function_expression>`  
  
   Representa um valor definido por um resultado de uma chamada de função.  
  
- `udf_scalar_function`  
  
   Nome da função escalar definida pelo utilizador.  
  
- `builtin_scalar_function`  
  
   Nome da função escalar incorporada.  
  
- `<create_object_expression>`  
  
   Representa um valor de obteve ao criar um novo objeto com propriedades especificadas e os respetivos valores.  
  
- `<create_array_expression>`  
  
   Representa um valor de obteve ao criar uma nova matriz com os valores especificados como elementos  
  
- `parameter_name`  
  
   Representa um valor do nome do parâmetro especificado. Os nomes dos parâmetros têm de ter um único \@ como o primeiro caráter.  
  
## <a name="remarks"></a>Observações
  
  Ao chamar uma função escalar incorporada ou definidas pelo utilizador, todos os argumentos devem ser definidos. Se qualquer um dos argumentos não está definido, a função não será chamada e o resultado será indefinido.  
  
  Ao criar um objeto, qualquer propriedade que é atribuída o valor indefinido será ignorada e não incluída no objeto criado.  
  
  Quando a criação de uma matriz, qualquer valor de elemento que é atribuído **indefinido** valor será ignorado e não incluído no objeto criado. Isso fará com que o próximo elemento definido que ocupará seu lugar, de forma que a matriz criada não irá ter ignorado índices.  

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

Na seguinte consulta, o resultado da expressão escalar é um booleano:

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)