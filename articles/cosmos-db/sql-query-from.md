---
title: Da cláusula em Azure Cosmos DB
description: Conheça a sintaxe SQL e, por exemplo, a cláusula FROM para o Azure Cosmos DB. Este artigo também mostra exemplos de resultados de âmbito de alcance, e obtém subitens usando a cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587690"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Da cláusula em Azure Cosmos DB

A cláusula FROM (`FROM <from_specification>`) é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta como `SELECT * FROM Families` enumera todo o recipiente `Families`. Também pode utilizar o identificador especial ROOT para o recipiente em vez de utilizar o nome do recipiente.

A cláusula FROM aplica as seguintes regras por consulta:

* O recipiente pode ser pseudónimo, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o pseudónimo para `Families`. As é uma palavra-chave opcional para [alias](sql-query-aliasing.md) o identificador.  

* Uma vez pseudónimo, o nome original não pode ser ligado. Por exemplo, `SELECT Families.id FROM Families f` é sintáticamente inválida porque o identificador `Families` foi pseudónimo e não pode mais ser resolvido.  

* Todas as propriedades referenciadas devem ser totalmente qualificadas, para evitar quaisquer encadernações ambíguas na ausência de estrita adesão ao esquema. Por exemplo, `SELECT id FROM Families f` é sintáticamente inválida porque a propriedade `id` não está vinculada.

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
  
  Especifica uma origem de dados, com ou sem um alias. Se o pseudónimo não for especificado, será inferido do `<container_expression>` utilizando as seguintes regras:  
  
  -  Se a expressão for um container_name, container_name será utilizado como um alias.  
  
  -  Se a expressão for `<container_expression>`, então property_name, então property_name será usada como pseudónimo. Se a expressão for um container_name, container_name será utilizado como um alias.  
  
- AS `input_alias`  
  
  Especifica que o `input_alias` é um conjunto de valores devolvidos pela expressão do recipiente subjacente.  
 
- `input_alias` IN  
  
  Especifica que o `input_alias` deve representar o conjunto de valores obtidos por iteração sobre todos os elementos matrizes de cada matriz devolvidos pela expressão do recipiente subjacente. Qualquer valor devolvido pela expressão subjacente do contentor que não seja uma matriz é ignorada.  
  
- `<container_expression>`  
  
  Especifica a expressão de contentor a ser usada para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que esse documento deve ser obtido da predefinição, o contentor atualmente ligada.  
  
- `container_name`  
  
  Especifica que esse documento deve ser obtido a partir do contentor fornecido. O nome do contentor tem de corresponder ao nome do contentor ligadas atualmente ao.  
  
- `input_alias`  
  
  Especifica que esse documento deve ser obtido a partir de outra origem definida pelo alias fornecido.  
  
- `<container_expression> '.' property_name`  
  
  Especifica que o documento deve ser recuperado acedendo à propriedade `property_name`.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que o documento deve ser recuperado acedendo à propriedade `property_name` ou elemento de matriz array_index para todos os documentos recuperados pela expressão especificada do recipiente.  
  
## <a name="remarks"></a>Observações
  
Todos os pseudónimos fornecidos ou inferidos na `<from_source>(`) devem ser únicos. O property_name de `<container_expression>.`sintaxe é o mesmo que `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe esse último pode ser utilizada se um nome de propriedade contém um caráter não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>manipulação em falta as propriedades, em falta elementos de matriz e os valores não definidos
  
Se uma expressão de contentor acessa as propriedades ou elementos de matriz e que valor não existir, esse valor será ignorado e não processado ainda mais.  
  
### <a name="container-expression-context-scoping"></a>O controlo de âmbito do contentor expressão contexto  
  
Uma expressão de contentor pode ser no âmbito do contentor ou no âmbito do documento:  
  
-   Uma expressão é de alcance de contentores, se a fonte subjacente da expressão do recipiente for RAIZ ou `container_name`. Tal uma expressão representa um conjunto de documentos obtidas diretamente a partir do contentor e não é dependente do processamento de outras expressões de contentor.  
  
-   Uma expressão é com um documento, se a fonte subjacente da expressão do recipiente for `input_alias` introduzida anteriormente na consulta. Tal uma expressão representa um conjunto de documentos obtido ao avaliar a expressão de contentor no âmbito de cada documento que pertencem ao conjunto associado ao contentor de um alias.  O conjunto resultante será uma União dos conjuntos de obteve ao avaliar a expressão de contentor para cada um dos documentos no conjunto de subjacente. 

## <a name="examples"></a>Exemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obtenha subitens usando a cláusula FROM

A cláusula FROM pode reduzir a fonte a um subconjunto menor. Para enumerar apenas uma subárvore em cada item, a subraiza pode tornar-se a fonte, como mostra o seguinte exemplo:

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

A consulta anterior usou uma matriz como fonte, mas também pode usar um objeto como fonte. A consulta considera qualquer valor JSON válido e definido na fonte de inclusão no resultado. O exemplo que se segue excluiria `Families` que não têm um valor `address.state`.

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
- [ONDE a cláusula](sql-query-where.md)
