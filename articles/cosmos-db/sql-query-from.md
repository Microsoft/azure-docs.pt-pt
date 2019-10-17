---
title: Cláusula FROM em Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL FROM para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327004"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Cláusula FROM em Azure Cosmos DB

A cláusula FROM (`FROM <from_specification>`) é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta como `SELECT * FROM Families` enumera em todo o contêiner `Families`. Você também pode usar a raiz do identificador especial para o contêiner em vez de usar o nome do contêiner.

A cláusula FROM impõe as seguintes regras por consulta:

* O contêiner pode ter um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias para `Families`. Como é uma palavra-chave opcional para [alias](sql-query-aliasing.md) do identificador.  

* Depois de alias, o nome de origem original não pode ser associado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` tem um alias e não pode mais ser resolvido.  

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
  
  Especifica uma fonte de dados, com ou sem um alias. Se o alias não for especificado, ele será inferido do `<container_expression>` usando as seguintes regras:  
  
  -  Se a expressão for um container_name, então container_name será usado como um alias.  
  
  -  Se a expressão for `<container_expression>`, então property_name, property_name será usado como um alias. Se a expressão for um container_name, então container_name será usado como um alias.  
  
- COMO `input_alias`  
  
  Especifica que o `input_alias` é um conjunto de valores retornados pela expressão de contêiner subjacente.  
 
- `input_alias` em  
  
  Especifica que o `input_alias` deve representar o conjunto de valores obtidos pela iteração em todos os elementos de matriz de cada matriz retornada pela expressão de contêiner subjacente. Qualquer valor retornado pela expressão de contêiner subjacente que não é uma matriz é ignorado.  
  
- `<container_expression>`  
  
  Especifica a expressão de contêiner a ser usada para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que o documento deve ser recuperado do contêiner padrão conectado no momento.  
  
- `container_name`  
  
  Especifica que o documento deve ser recuperado do contêiner fornecido. O nome do contêiner deve corresponder ao nome do contêiner atualmente conectado.  
  
- `input_alias`  
  
  Especifica que o documento deve ser recuperado da outra fonte definida pelo alias fornecido.  
  
- `<container_expression> '.' property_`  
  
  Especifica que o documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que o documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
## <a name="remarks"></a>Observações
  
Todos os aliases fornecidos ou inferidos no `<from_source>(`s) devem ser exclusivos. A sintaxe `<container_expression>.`property_name é a mesma que `<container_expression>' ['"property_name"']'`. No entanto, a última sintaxe poderá ser usada se um nome de propriedade contiver um caractere não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manipulando propriedades ausentes, elementos de matriz ausentes e valores indefinidos
  
Se uma expressão de contêiner acessar propriedades ou elementos de matriz e esse valor não existir, esse valor será ignorado e não poderá mais ser processado.  
  
### <a name="container-expression-context-scoping"></a>Escopo de contexto de expressão de contêiner  
  
Uma expressão de contêiner pode ser no escopo do contêiner ou no escopo do documento:  
  
-   Uma expressão é de escopo de contêiner, se a fonte subjacente da expressão de contêiner for ROOT ou `container_name`. Essa expressão representa um conjunto de documentos recuperados diretamente do contêiner e não depende do processamento de outras expressões de contêiner.  
  
-   Uma expressão tem escopo de documento, se a fonte subjacente da expressão de contêiner for `input_alias` introduzida anteriormente na consulta. Essa expressão representa um conjunto de documentos obtidos pela avaliação da expressão de contêiner no escopo de cada documento pertencente ao conjunto associado ao contêiner com alias.  O conjunto resultante será uma União de conjuntos obtidos pela avaliação da expressão de contêiner para cada um dos documentos no conjunto subjacente. 

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

A consulta anterior usou uma matriz como a origem, mas você também pode usar um objeto como a origem. A consulta considera qualquer valor JSON válido e definido na fonte para inclusão no resultado. O exemplo a seguir excluirá `Families` que não têm um valor de `address.state`.

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