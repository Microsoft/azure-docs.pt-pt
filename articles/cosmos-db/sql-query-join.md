---
title: Consultas SQL JOIN para o Azure Cosmos DB
description: Saiba mais sobre a sintaxe de associação SQL para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342862"
---
# <a name="joins-in-azure-cosmos-db"></a>Associa no Azure Cosmos DB

Numa base de dados relacional, as associações entre tabelas são o resultado de lógico para a criação de esquemas normalizadas. Por outro lado, a API de SQL utiliza o modelo de dados desnormalizados de itens sem esquema, que é lógico equivalente de um *associação automática*.

Associações internas resultam num produto cruzado completo dos conjuntos de participar na União. O resultado de uma junção N-vias é um conjunto de cadeias de identificação de elemento de N, onde cada valor na cadeia de identificação é associado o alias definido a participar na União e pode ser acedido ao consultar esse alias em outras cláusulas.

## <a name="syntax"></a>Sintaxe

A linguagem suporta a sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Esta consulta devolve um conjunto de tuplas com `N` valores. Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos. 

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

Os exemplos seguintes mostram como funciona a cláusula JOIN. No exemplo a seguir, o resultado está vazio, desde o produto cruzado de cada item de origem e um conjunto vazio está vazio:

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

No exemplo seguinte, a associação é um produto cruzado entre dois objetos JSON, a raiz do item `id` e o `children` subroot. O fato de que `children` é uma matriz não é eficaz na associação, como ela lida com uma raiz única que é o `children` matriz. O resultado contém apenas dois resultados, uma vez que o produto cruzado de cada item com a matriz produz exatamente a apenas um item.

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

A fonte FROM da cláusula JOIN é um iterador. Então, o fluxo no exemplo anterior é:  

1. Expanda cada elemento subordinado `c` na matriz.
2. Aplicam-se de um produto cruzado com a raiz do item `f` com cada elemento filho `c` que o primeiro passo simplificado.
3. Por fim, o objeto raiz do projeto `f` `id` propriedade individualmente.

O primeiro item `AndersenFamily`, contém apenas um `children` elemento, para que o conjunto de resultados contém apenas um único objeto. O segundo item `WakefieldFamily`, contém dois `children`, para que o produto cruzado produz dois objetos, um para cada `children` elemento. Os campos de raiz em ambos os esses itens são iguais, tal como se poderia esperar num produto cruzado.

O utilitário real da cláusula de JUNÇÃO é formulário cadeias de identificação do produto numa forma que caso contrário, é difícil de projeto. O exemplo abaixo filtros a combinação de uma cadeia de identificação que permite que o usuário escolha uma condição satisfeito pelas tuplas geral.

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

A extensão seguinte do exemplo anterior efetua uma junção dupla. Pode ver o produto cruzado como o seguinte pseudocódigo:

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

`AndersenFamily` tem um subordinado que tenha um animal de estimação, para que o produto cruzado produz uma linha (1\*1\*1) desta família. `WakefieldFamily` tem dois filhos, apenas um dos quais tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para esta família produz 1\*1\*2 = 2 linhas.

No exemplo seguinte, existe um filtro adicional no `pet`, que exclui todas as cadeias de identificação em que o nome do animal de estimação não é `Shadow`. Pode criar cadeias de identificação de matrizes, filtro em qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto.

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)