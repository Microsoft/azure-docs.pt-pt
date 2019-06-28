---
title: DA cláusula no Azure Cosmos DB
description: Saiba mais sobre o SQL a cláusula FROM para o Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342439"
---
# <a name="from-clause"></a>Cláusula FROM

Campo FROM (`FROM <from_specification>`) cláusula é opcional, a menos que a origem é filtrada ou prevista mais tarde na consulta. Uma consulta, como `SELECT * FROM Families` enumera sobre toda a `Families` contentor. Também pode utilizar o identificador especial raiz para o contentor em vez de utilizar o nome do contentor.

A cláusula FROM impõe as seguintes regras por consulta:

* O contentor pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias do `Families`. COMO é uma palavra-chave opcional para [alias](sql-query-aliasing.md) o identificador.  

* Uma vez, um alias, não é possível vincular o nome de origem original. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválida porque o identificador `Families` tem sido um alias e não pode ser resolvido mais.  

* Todas as propriedades referenciadas tem de ser totalmente qualificadas evitar qualquer vinculação ambígua na ausência da aderência do esquema restrito. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválida porque a propriedade `id` não está vinculado.

## <a name="syntax"></a>Sintaxe
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<from_source>`  
  
  Especifica uma origem de dados, com ou sem um alias. Se não for especificado o alias, ele irá ser inferido a partir do `<container_expression>` com os seguintes regras:  
  
  -  Se a expressão for um container_name, container_name será utilizado como um alias.  
  
  -  Se a expressão for `<container_expression>`, property_name, em seguida, property_name será utilizado como um alias. Se a expressão for um container_name, container_name será utilizado como um alias.  
  
- AS `input_alias`  
  
  Especifica que o `input_alias` é um conjunto de valores devolvidos pela expressão de contentor subjacente.  
 
- `input_alias` ÍNDIA  
  
  Especifica que o `input_alias` deve representar o conjunto de valores obtidos com a iteração sobre todos os elementos de matriz de cada matriz devolvida pela expressão de contentor subjacente. Qualquer valor devolvido pela expressão subjacente do contentor que não seja uma matriz é ignorada.  
  
- `<container_expression>`  
  
  Especifica a expressão de contentor a ser usada para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que esse documento deve ser obtido da predefinição, o contentor atualmente ligada.  
  
- `container_name`  
  
  Especifica que esse documento deve ser obtido a partir do contentor fornecido. O nome do contentor tem de corresponder ao nome do contentor ligadas atualmente ao.  
  
- `input_alias`  
  
  Especifica que esse documento deve ser obtido a partir de outra origem definida pelo alias fornecido.  
  
- `<container_expression> '.' property_`  
  
  Especifica esse documento deve ser obtido ao aceder a `property_name` especificada de propriedade ou array_index elemento de matriz para todos os documentos obtidos através de expressão de contentor.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica esse documento deve ser obtido ao aceder a `property_name` especificada de propriedade ou array_index elemento de matriz para todos os documentos obtidos através de expressão de contentor.  
  
## <a name="remarks"></a>Observações
  
Todos os aliases fornecido ou inferido no `<from_source>(`s) tem de ser exclusivo. A sintaxe `<container_expression>.`property_name é o mesmo que `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe esse último pode ser utilizada se um nome de propriedade contém um caráter não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>manipulação em falta as propriedades, em falta elementos de matriz e os valores não definidos
  
Se uma expressão de contentor acessa as propriedades ou elementos de matriz e que valor não existir, esse valor será ignorado e não processado ainda mais.  
  
### <a name="container-expression-context-scoping"></a>O controlo de âmbito do contentor expressão contexto  
  
Uma expressão de contentor pode ser no âmbito do contentor ou no âmbito do documento:  
  
-   Uma expressão é o contentor no âmbito, se a origem subjacente da expressão de contentor é a raiz ou `container_name`. Tal uma expressão representa um conjunto de documentos obtidas diretamente a partir do contentor e não é dependente do processamento de outras expressões de contentor.  
  
-   Uma expressão é o documento no âmbito, se a origem subjacente da expressão de contentor é `input_alias` introduzida anteriormente a consulta. Tal uma expressão representa um conjunto de documentos obtido ao avaliar a expressão de contentor no âmbito de cada documento que pertencem ao conjunto associado ao contentor de um alias.  O conjunto resultante será uma União dos conjuntos de obteve ao avaliar a expressão de contentor para cada um dos documentos no conjunto de subjacente. 

## <a name="examples"></a>Exemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obtenha subitems, utilizando a cláusula FROM

A cláusula FROM pode reduzir a origem para um subconjunto mais pequeno. Para enumerar apenas uma subárvore de cada item, o subroot pode tornar-se a origem, conforme mostrado no exemplo a seguir:

```sql
    SELECT *
    FROM Families.children
```

Os resultados são:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

A consulta anterior utilizado uma matriz, como a origem, mas também pode utilizar um objeto como a origem. A consulta considera qualquer valor JSON válido, definido na origem para inclusão no resultado. O exemplo seguinte seria excluir `Families` que não têm um `address.state` valor.

```sql
    SELECT *
    FROM Families.address.state
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)