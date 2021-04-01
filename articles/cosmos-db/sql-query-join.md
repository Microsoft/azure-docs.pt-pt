---
title: SQL JOIN consultas para Azure Cosmos DB
description: Saiba como juntar várias tabelas em Azure Cosmos DB para consultar os dados
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: tisande
ms.openlocfilehash: cb7b2e62a9fabeeca675edb8e6aa356213e0999e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011410"
---
# <a name="joins-in-azure-cosmos-db"></a>Junta-se ao Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Numa base de dados relacional, as juntas entre as tabelas são o corolário lógico para a conceção de esquemas normalizados. Em contraste, a API SQL utiliza o modelo de dados desnormalizado de itens isentos de esquemas, que é o equivalente lógico de uma *união auto-união*.

As junções resultam num produto cruzado completo dos conjuntos participantes na junção. O resultado de uma junção N-way é um conjunto de tuples de elemento N, onde cada valor no tuple está associado com o conjunto pseudónimo que participa na junção e pode ser acedido referindo esse pseudónimo em outras cláusulas.

## <a name="syntax"></a>Syntax

A língua suporta a `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` sintaxe. Esta consulta devolve um conjunto de tuples com `N` valores. Cada tuple tem valores produzidos através da iterando todos os pseudónimos dos recipientes sobre os respetivos conjuntos. 

Vejamos a seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Que cada fonte `input_alias1, input_alias2, …, input_aliasN` defina. Esta cláusula FROM devolve um conjunto de N-tuples (tuple com valores N). Cada tuple tem valores produzidos através da iterando todos os pseudónimos dos recipientes sobre os respetivos conjuntos.  
  
**Exemplo 1** - 2 fontes  
  
- `<from_source1>`Deixe-nos ser telescópios e represente o conjunto {A, B, C}.  
  
- Sejamos `<from_source2>` referenciados por documentos input_alias1 e represente conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nos seguintes tuples:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** - 3 fontes  
  
- `<from_source1>`Deixe-nos ser telescópios e represente o conjunto {A, B, C}.  
  
- Sejamos `<from_source2>` referenciados e `input_alias1` represente conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Sejamos `<from_source3>` referenciados e `input_alias2` represente conjuntos:  
  
    {100, 200} para `input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nos seguintes tuples:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1,100), (A, 1,200), (B, 3,300)  
  
  > [!NOTE]
  > Falta de tuples para outros valores `input_alias1` `input_alias2` de, para os quais os `<from_source3>` não devolveram quaisquer valores.  
  
**Exemplo 3** - 3 fontes  
  
- Deixe <from_source1> ser telescópio de contentores e represente o conjunto {A, B, C}.  
  
- `<from_source1>`Deixe-nos ser telescópios e represente o conjunto {A, B, C}.  
  
- Deixe <from_source2> ser referenciado por documentos input_alias1 e represente conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Vamos `<from_source3>` ser examinados `input_alias1` e representar conjuntos:  
  
    {100, 200} para `input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nos seguintes tuples:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1,100), (A, 1,200), (A, 2, 100), (A, 2, 200), (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Isto resultou num produto transversal entre `<from_source2>` e porque ambos são `<from_source3>` abrangidos pelo mesmo `<from_source1>` .  Isto resultou em tuples de 4 (2x2) com valor A, 0 tuples com valor B (1x0) e 2 (2x1) tuples com valor C.  
  
## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como funciona a cláusula JOIN. Antes de executar estes exemplos, carre faça o upload dos [dados da família](sql-query-getting-started.md#upload-sample-data)da amostra. No exemplo seguinte, o resultado está vazio, uma vez que o produto transversal de cada item proveniente da fonte e um conjunto vazio estão vazios:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

O resultado é:

```json
    [{
    }]
```

No exemplo seguinte, a junção é um produto cruzado entre dois objetos JSON, a raiz do item `id` e a `children` subroot. O facto de ser `children` uma matriz não é eficaz na junção, porque lida com uma única raiz que é a `children` matriz. O resultado contém apenas dois resultados, porque o produto transversal de cada item com a matriz produz exatamente apenas um item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

O exemplo a seguir mostra uma junção mais convencional:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

A fonte da cláusula JOIN é um iterador. Assim, o fluxo no exemplo anterior é:  

1. Expanda cada elemento infantil `c` na matriz.
2. Aplique um produto cruzado com a raiz do item `f` com cada elemento de criança que o primeiro passo `c` achatado.
3. Finalmente, projete a propriedade do objeto raiz `f` `id` sozinho.

O primeiro item, `AndersenFamily` contém apenas um `children` elemento, pelo que o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily` contém `children` dois, de modo que o produto transversal produz dois objetos, um para cada `children` elemento. Os campos de raiz em ambos os itens são os mesmos, tal como seria de esperar num produto transversal.

A verdadeira utilidade da cláusula JOIN é formar tuples a partir do produto transversal de uma forma que seja difícil de projetar. O exemplo abaixo filtra-se na combinação de um tuple que permite ao utilizador escolher uma condição satisfeita pelos tuples em geral.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Os resultados são:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

A seguinte extensão do exemplo anterior executa uma dupla junção. Pode ver o produto cruzado como o seguinte pseudo-código:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` tem um filho que tem um animal de estimação, por isso o produto cruzado rende uma linha \* (1 \* 11) desta família. `WakefieldFamily` tem dois filhos, apenas um dos quais tem animais de estimação, mas a criança tem dois animais de estimação. O produto transversal para esta família rende 1 \* \* 12 = 2 linhas.

No exemplo seguinte, existe um filtro adicional, `pet` que exclui todos os tuples onde o nome de estimação não é `Shadow` . Você pode construir tuples a partir de matrizes, filtrar em qualquer um dos elementos do tuple, e projetar qualquer combinação dos elementos.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Os resultados são:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Se a sua consulta tiver um JOIN e filtros, pode reescrever parte da consulta como [subconsejo para](sql-query-subquery.md#optimize-join-expressions) melhorar o desempenho.

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)