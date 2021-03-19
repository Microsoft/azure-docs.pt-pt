---
title: Cláusula de Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL, e exemplo para a cláusula FROM para Azure Cosmos DB. Este artigo também mostra exemplos para os resultados do âmbito e obter sub-itens usando a cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 3844a628a8e3773821736324bfdaab3f64ff9da5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336287"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Cláusula de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A cláusula FROM `FROM <from_specification>` () é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta como `SELECT * FROM Families` enumera sobre todo o `Families` contentor. Também pode utilizar o identificador especial ROOT para o recipiente em vez de utilizar o nome do recipiente.

A `FROM` cláusula aplica as seguintes regras por consulta:

* O recipiente pode ser aliasado, tal como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f` . Aqui `f` está o pseudónimo `Families` de. O AS é uma palavra-chave opcional para [o pseudónimo](sql-query-working-with-json.md#aliasing) do identificador.  

* Uma vez aliased, o nome de origem original não pode ser vinculado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` foi aliasado e não pode mais ser resolvido.  

* Todas as propriedades referenciadas devem ser totalmente qualificadas, além de quaisquer encadernações ambíguas na ausência de uma adesão rigorosa ao esquema. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválido porque a propriedade `id` não está ligada.

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
  
  Especifica uma fonte de dados, com ou sem pseudónimo. Se o pseudónimo não for especificado, será deduzido das `<container_expression>` seguintes regras:  
  
-  Se a expressão for uma container_name, então container_name será usado como pseudónimo.  
  
-  Se a expressão for `<container_expression>` , então property_name, então property_name será usado como pseudónimo. Se a expressão for uma container_name, então container_name será usado como pseudónimo.  
  
- AS `input_alias`  
  
  Especifica que `input_alias` é um conjunto de valores devolvidos pela expressão do recipiente subjacente.  
 
- `input_alias` EM  
  
  Especifica que `input_alias` o deve representar o conjunto de valores obtidos por iteramento sobre todos os elementos de matriz de cada matriz devolvidos pela expressão do recipiente subjacente. Qualquer valor devolvido pela expressão subjacente do recipiente que não seja uma matriz é ignorado.  
  
- `<container_expression>`  
  
  Especifica a expressão do recipiente a utilizar para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que o documento deve ser recuperado do recipiente padrão, atualmente ligado.  
  
- `container_name`  
  
  Especifica que o documento deve ser recuperado do recipiente fornecido. O nome do recipiente deve corresponder ao nome do recipiente atualmente ligado.  
  
- `input_alias`  
  
  Especifica que o documento deve ser recuperado a partir da outra fonte definida pelo pseudónimo fornecido.  
  
- `<container_expression> '.' property_name`  
  
  Especifica que o documento deve ser recuperado acedendo ao `property_name` imóvel.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que o documento deve ser recuperado acedendo à `property_name` propriedade ou array_index elemento de matriz para todos os documentos recuperados por expressão de recipiente especificado.  
  
## <a name="remarks"></a>Observações
  
Todos os pseudónimos fornecidos ou inferidos no `<from_source>(` s) devem ser únicos. O property_name de Sintaxe `<container_expression>.` é o mesmo que . `<container_expression>' ['"property_name"']'` . No entanto, esta última sintaxe pode ser usada se um nome de propriedade contiver um carácter não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manuseamento de propriedades em falta, elementos de matriz em falta e valores indefinidos
  
Se uma expressão de contentor aceder a propriedades ou elementos de matriz e esse valor não existir, esse valor será ignorado e não processado mais.  
  
### <a name="container-expression-context-scoping"></a>Scoping de contexto de expressão de recipiente  
  
Uma expressão de contentor pode ser incluída no âmbito do contentor ou no âmbito do documento:  
  
- Uma expressão é de âmbito de contentor, se a fonte subjacente da expressão do recipiente for RAIZ ou `container_name` . . Esta expressão representa um conjunto de documentos extraídos diretamente do contentor e não depende do processamento de outras expressões de contentores.  
  
- Uma expressão é identificada em documentos, se a fonte subjacente à expressão do recipiente for `input_alias` introduzida anteriormente na consulta. Esta expressão representa um conjunto de documentos obtidos através da avaliação da expressão do recipiente no âmbito de cada documento pertencente ao conjunto associado ao recipiente aliased. O conjunto resultante será uma união de conjuntos obtidos avaliando a expressão do recipiente para cada um dos documentos do conjunto subjacente.

## <a name="examples"></a>Exemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obtenha subínmas usando a cláusula FROM

A cláusula FROM pode reduzir a fonte a um subconjunto menor. Para enumerar apenas uma subtree em cada item, a subroota pode tornar-se a fonte, como mostra o seguinte exemplo:

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

A consulta anterior usou uma matriz como fonte, mas também pode usar um objeto como fonte. A consulta considera qualquer valor JSON válido e definido na fonte para inclusão no resultado. O exemplo seguinte excluiria `Families` que não têm `address.state` valor.

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
