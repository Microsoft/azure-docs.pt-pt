---
title: Cláusula FROM em Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL e o exemplo da cláusula FROM para Azure Cosmos DB. Este artigo também mostra exemplos de escopo de resultados e obter subitens usando a cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 4f6d7580ea7ff0e8968c0c3ce4b3ca6111c86ac8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873374"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Cláusula FROM em Azure Cosmos DB

A cláusula FROM (`FROM <from_specification>`) é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta como `SELECT * FROM Families` enumera em todo o contêiner de `Families`. Você também pode usar a raiz do identificador especial para o contêiner em vez de usar o nome do contêiner.

A cláusula FROM impõe as seguintes regras por consulta:

* O contentor pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias para `Families`. Como é uma palavra-chave opcional para [alias](sql-query-aliasing.md) do identificador.  

* Depois de alias, o nome de origem original não pode ser associado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` foi alias e não pode mais ser resolvido.  

* Todas as propriedades referenciadas devem ser totalmente qualificadas para evitar associações ambíguas na ausência da adesão estrita ao esquema. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválido porque a propriedade `id` não está associada.

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

### <a name="get-subitems-by-using-the-from-clause"></a>Obter subitens usando a cláusula FROM

A cláusula FROM pode reduzir a origem para um subconjunto menor. Para enumerar apenas uma subárvore em cada item, a subraiz pode se tornar a origem, conforme mostrado no exemplo a seguir:

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

A consulta anterior usou uma matriz como a origem, mas você também pode usar um objeto como a origem. A consulta considera qualquer valor JSON válido e definido na fonte para inclusão no resultado. O exemplo a seguir excluiria `Families` que não têm um valor de `address.state`.

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)