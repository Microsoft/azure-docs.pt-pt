---
title: Da cláusula em Azure Cosmos DB
description: Conheça a sintaxe SQL e, por exemplo, a cláusula FROM para o Azure Cosmos DB. Este artigo também mostra exemplos de resultados de âmbito de alcance, e obtém subitens usando a cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587690"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Da cláusula em Azure Cosmos DB

A cláusula`FROM <from_specification>`FROM ( ) é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta `SELECT * FROM Families` como enumera sobre `Families` todo o recipiente. Também pode utilizar o identificador especial ROOT para o recipiente em vez de utilizar o nome do recipiente.

A cláusula FROM aplica as seguintes regras por consulta:

* O recipiente pode ser pseudónimo, `SELECT f.id FROM Families AS f` como `SELECT f.id FROM Families f`ou simplesmente . Aqui `f` está o `Families`pseudónimo para. As é uma palavra-chave opcional para [alias](sql-query-aliasing.md) o identificador.  

* Uma vez pseudónimo, o nome original não pode ser ligado. Por exemplo, `SELECT Families.id FROM Families f` é sintáticamente inválido `Families` porque o identificador foi pseudónimo e não pode mais ser resolvido.  

* Todas as propriedades referenciadas devem ser totalmente qualificadas, para evitar quaisquer encadernações ambíguas na ausência de estrita adesão ao esquema. Por exemplo, `SELECT id FROM Families f` é sintáticamente inválido porque a propriedade `id` não está ligada.

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
  
  Especifica uma fonte de dados, com ou sem pseudónimo. Se o pseudónimo não for especificado, será `<container_expression>` inferido da utilização das seguintes regras:  
  
  -  Se a expressão for uma container_name, então container_name serão usadas como pseudónimo.  
  
  -  Se a `<container_expression>`expressão for , então property_name, então property_name será usada como pseudónimo. Se a expressão for uma container_name, então container_name serão usadas como pseudónimo.  
  
- COMO`input_alias`  
  
  Especifica que `input_alias` se trata de um conjunto de valores devolvidos pela expressão do recipiente subjacente.  
 
- `input_alias`EM  
  
  Especifica que `input_alias` o conjunto de valores obtidos por iteração sobre todos os elementos matrizes de cada matriz devolvidopela expressão do recipiente subjacente. Qualquer valor devolvido pela expressão subjacente do recipiente que não seja uma matriz é ignorado.  
  
- `<container_expression>`  
  
  Especifica a expressão do recipiente a utilizar para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que o documento deve ser recuperado do recipiente por defeito, atualmente ligado.  
  
- `container_name`  
  
  Especifica que o documento deve ser recuperado do recipiente fornecido. O nome do recipiente deve coincidir com o nome do recipiente atualmente ligado.  
  
- `input_alias`  
  
  Especifica que o documento deve ser recuperado da outra fonte definida pelo pseudónimo fornecido.  
  
- `<container_expression> '.' property_name`  
  
  Especifica que o documento deve ser `property_name` recuperado acedendo à propriedade.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que o documento deve ser `property_name` recuperado acedendo à propriedade ou array_index elemento matriz para todos os documentos recuperados pela expressão especificada do recipiente.  
  
## <a name="remarks"></a>Observações
  
Todos os pseudónimos fornecidos ou `<from_source>(`inferidos no s) devem ser únicos. A property_name `<container_expression>.`sintaxe `<container_expression>' ['"property_name"']'`é a mesma que. No entanto, esta última sintaxe pode ser utilizada se um nome de propriedade contiver um carácter não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manuseamento de propriedades em falta, elementos de matriz em falta e valores indefinidos
  
Se uma expressão de contentor aceder a propriedades ou elementos de matriz e esse valor não existir, esse valor será ignorado e não será processado mais.  
  
### <a name="container-expression-context-scoping"></a>Deteção de contexto de expressão de recipiente  
  
Uma expressão de contentor pode ser de contentorou ou com um documento:  
  
-   Uma expressão é de alcance de contentores, se a `container_name`fonte subjacente da expressão do recipiente for RAIZ ou . Tal expressão representa um conjunto de documentos recuperados diretamente do contentor, e não depende do processamento de outras expressões de contentores.  
  
-   Uma expressão é com um documento, se a `input_alias` fonte subjacente da expressão do recipiente for introduzida mais cedo na consulta. Tal expressão representa um conjunto de documentos obtidos através da avaliação da expressão do contentor no âmbito de cada documento pertencente ao conjunto associado ao recipiente aliado.  O conjunto resultante será uma união de conjuntos obtidos avaliando a expressão do contentor para cada um dos documentos no conjunto subjacente. 

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

A consulta anterior usou uma matriz como fonte, mas também pode usar um objeto como fonte. A consulta considera qualquer valor JSON válido e definido na fonte de inclusão no resultado. O exemplo que `Families` se segue excluiria que não têm valor. `address.state`

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

- [Começar](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [ONDE a cláusula](sql-query-where.md)
