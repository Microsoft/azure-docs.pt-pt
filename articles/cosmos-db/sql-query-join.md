---
title: Consultas do SQL JOIN para Azure Cosmos DB
description: Saiba como unir várias tabelas em Azure Cosmos DB para consultar os dados
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871147"
---
# <a name="joins-in-azure-cosmos-db"></a>Junções no Azure Cosmos DB

Em um banco de dados relacional, as junções entre as tabelas são o registro lógico da criação de esquemas normalizados. Por outro lado, a API do SQL usa o modelo de dados desnormalizado de itens sem esquema, que é o equivalente lógico de uma *auto-associação*.

Associações internas resultam num produto cruzado completo dos conjuntos de participar na União. O resultado de uma junção N-vias é um conjunto de cadeias de identificação de elemento de N, onde cada valor na cadeia de identificação é associado o alias definido a participar na União e pode ser acedido ao consultar esse alias em outras cláusulas.

## <a name="syntax"></a>Sintaxe

O idioma dá suporte à sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Essa consulta retorna um conjunto de tuplas com `N` valores. Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos. 

Vejamos o seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permitir que cada origem de definir `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM devolve um conjunto de tuplas de N (cadeia de identificação com valores de N). Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos.  
  
**Exemplo 1** -2 origens  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitir que `<from_source2>` ser documentos no âmbito referenciar input_alias1 e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará em cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** -3 origens  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitem `<from_source2>` ser no âmbito do documento referenciar `input_alias1` e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Permitem `<from_source3>` ser no âmbito do documento referenciar `input_alias2` e representam conjuntos:  
  
    {100, 200} para `input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará em cadeias de identificação seguintes:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Falta de cadeias de identificação de outros valores de `input_alias1`, `input_alias2`, para o qual o `<from_source3>` não devolveu quaisquer valores.  
  
**Exemplo 3** -3 origens  
  
- Permitir que < from_source1 > ser no âmbito do contentor e representam conjunto {A, B, C}.  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitir que < from_source2 > ser input_alias1 de referência no âmbito do documento e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Permitem `<from_source3>` confinados `input_alias1` e representam conjuntos:  
  
    {100, 200} para `input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará em cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` uma vez que ambos estão no âmbito da mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) as tuplas ter valor A, as tuplas 0 ter valor B (1 x 0) e 2 (2 x 1) as tuplas ter valor C.  
  
## <a name="examples"></a>Exemplos

Os exemplos seguintes mostram como funciona a cláusula JOIN. Antes de executar esses exemplos, carregue os [dados da família](sql-query-getting-started.md#upload-sample-data)de exemplo. No exemplo a seguir, o resultado está vazio, pois o produto cruzado de cada item da origem e um conjunto vazio está vazio:

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

No exemplo a seguir, a junção é um produto cruzado entre dois objetos JSON, a raiz do item `id` e a subraiz `children`. O fato de que `children` é uma matriz não é eficaz na junção, pois lida com uma única raiz que é a matriz de `children`. O resultado contém apenas dois resultados, porque o produto cruzado de cada item com a matriz produz exatamente apenas um item.

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

O exemplo seguinte mostra uma junção mais convencional:

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

A origem da cláusula de junção é um iterador. Portanto, o fluxo no exemplo anterior é:  

1. Expanda cada elemento filho `c` na matriz.
2. Aplique um produto cruzado com a raiz do item `f` com cada elemento filho `c` que a primeira etapa seja nivelada.
3. Por fim, projeto o objeto raiz `f` `id` Propriedade sozinha.

O primeiro item, `AndersenFamily`, contém apenas um elemento `children`, portanto, o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily`, contém dois `children`, portanto, o produto cruzado produz dois objetos, um para cada elemento `children`. Os campos de raiz em ambos os esses itens são iguais, tal como se poderia esperar num produto cruzado.

O verdadeiro utilitário da cláusula JOIN é formar tuplas do produto cruzado em uma forma que seja difícil de projetar. O exemplo a seguir filtra na combinação de uma tupla que permite que o usuário escolha uma condição satisfeita pelas tuplas como um todo.

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

A seguinte extensão do exemplo anterior executa uma junção dupla. Você pode exibir o produto cruzado como o seguinte pseudocódigo:

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

`AndersenFamily` tem um filho que tem um animal de estimação, portanto, o produto cruzado produz uma linha (1\*1\*1) desta família. `WakefieldFamily` tem dois filhos, apenas um deles tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para esta família gera 1\*1\*2 = 2 linhas.

No próximo exemplo, há um filtro adicional em `pet`, que exclui todas as tuplas em que o nome do animal de estimação não é `Shadow`. Você pode criar tuplas de matrizes, filtrar em qualquer um dos elementos da tupla e projetar qualquer combinação dos elementos.

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)