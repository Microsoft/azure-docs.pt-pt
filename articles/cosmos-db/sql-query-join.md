---
title: SQL JOIN consultas para Azure Cosmos DB
description: Saiba como juntar várias tabelas no Azure Cosmos DB para consultar os dados
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871147"
---
# <a name="joins-in-azure-cosmos-db"></a>Junta-se ao Azure Cosmos DB

Numa base de dados relacional, as juntas entre mesas são o corolário lógico para conceber esquemas normalizados. Em contrapartida, a API SQL utiliza o modelo de dados desnormalizado de itens isentos de esquemas, que é o equivalente lógico de uma *auto-adesão*.

Juntas interiores resultam num produto cruzado completo dos conjuntos participantes na adesão. O resultado de uma adesão n-way é um conjunto de tuples de elemento N, onde cada valor na tuple está associado ao conjunto pseudónimo que participa na adesão e pode ser acedido referindo esse pseudónimo em outras cláusulas.

## <a name="syntax"></a>Sintaxe

A linguagem suporta a `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`sintaxe. Esta consulta devolve um conjunto de `N` tupículos com valores. Cada tuple tem valores produzidos por iterar todos os pseudónimos de contentores sobre os respetivos conjuntos. 

Vamos ver a seguinte cláusula FROM:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Deixe que `input_alias1, input_alias2, …, input_aliasN`cada fonte defina . Esta cláusula FROM devolve um conjunto de N-tuples (tuple com valores N). Cada tuple tem valores produzidos por iterar todos os pseudónimos de contentores sobre os respetivos conjuntos.  
  
**Exemplo 1** - 2 fontes  
  
- Deixe-o `<from_source1>` ser remeto e represente o conjunto {A, B, C}.  
  
- Deixe-se `<from_source2>` fazer referência seleto ao documento input_alias1 e representar conjuntos:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3}para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- A cláusula `<from_source1> JOIN <from_source2>` FROM resultará nos seguintes tuples:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** - 3 fontes  
  
- Deixe-o `<from_source1>` ser remeto e represente o conjunto {A, B, C}.  
  
- Deixe-se `<from_source2>` fazer referência `input_alias1` seleto ao documento e representar conjuntos:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3}para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- Deixe-se `<from_source3>` fazer referência `input_alias2` seleto ao documento e representar conjuntos:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300}para`input_alias2 = 3,`  
  
- A cláusula `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM resultará nos seguintes tuples:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    A, 1,100), A, 1,200), B, 3.300)  
  
  > [!NOTE]
  > Falta de tuples para `input_alias1` `input_alias2`outros valores `<from_source3>` de, para os quais os não devolveram quaisquer valores.  
  
**Exemplo 3** - 3 fontes  
  
- Deixe <from_source1> ser de alcance de contentores e representar o conjunto {A, B, C}.  
  
- Deixe-o `<from_source1>` ser remeto e represente o conjunto {A, B, C}.  
  
- Que <from_source2> ser referenciado por documentos input_alias1 e represente conjuntos:  
  
    {1, 2} para`input_alias1 = A,`  
  
    {3}para`input_alias1 = B,`  
  
    {4, 5} para`input_alias1 = C,`  
  
- Vamos `<from_source3>` ser examinados `input_alias1` e representar conjuntos:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300}para`input_alias2 = C,`  
  
- A cláusula `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM resultará nos seguintes tuples:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    A, 1,100), A, 1, 200), A, 2, 100), A, 2, 200), C, 4, 300, C, 5, 300)  
  
  > [!NOTE]
  > Isto resultou num `<from_source2>` produto `<from_source3>` cruzado entre e porque `<from_source1>`ambos são orientados para o mesmo .  Isto resultou em tuples de 4 (2x2) com valor A, 0 tuples com valor B (1x0) e 2 (2x1) tuples com valor C.  
  
## <a name="examples"></a>Exemplos

Os exemplos seguintes mostram como funciona a cláusula JOIN. Antes de executar estes exemplos, faça upload dos [dados da família](sql-query-getting-started.md#upload-sample-data)da amostra . No exemplo seguinte, o resultado está vazio, uma vez que o produto transversal de cada item de origem e um conjunto vazio está vazio:

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

No exemplo seguinte, a união é um produto cruzado entre `id` dois `children` objetos JSON, a raiz do item e a subraiza. O facto `children` de ser uma matriz não é eficaz na união, `children` porque lida com uma única raiz que é a matriz. O resultado contém apenas dois resultados, porque o produto transversal de cada item com a matriz produz exatamente apenas um item.

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

O exemplo que se segue mostra uma adesão mais convencional:

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

A fonte da cláusula JOIN é um iterator. Assim, o fluxo no exemplo anterior é:  

1. Expanda cada `c` elemento infantil na matriz.
2. Aplique um produto transversal com a `f` raiz do `c` item com cada elemento infantil que o primeiro passo achatado.
3. Finalmente, projete `f` `id` a propriedade do objeto raiz sozinho.

O primeiro item, `AndersenFamily`contém `children` apenas um elemento, pelo que o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily` `children`contém dois, assim o produto transversal produz `children` dois objetos, um para cada elemento. Os campos de raiz em ambos os itens são os mesmos, tal como seria de esperar num produto transversal.

A verdadeira utilidade da cláusula JOIN é formar tuples do produto transversal numa forma que de outra forma é difícil de projetar. O exemplo abaixo filtra a combinação de uma tuple que permite ao utilizador escolher uma condição satisfeita pelas tupículas em geral.

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

A seguinte extensão do exemplo anterior executa uma dupla adesão. Pode ver o produto transversal como o seguinte pseudo-código:

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

`AndersenFamily`tem um filho que tem um animal de estimação, por isso o produto cruzado produz uma linha (1\*1 1\*1) desta família. `WakefieldFamily`tem dois filhos, apenas um dos quais tem animais de estimação, mas essa criança tem dois animais de estimação. O produto transversal para\*esta\*família produz 1 12 = 2 linhas.

No exemplo seguinte, há um `pet`filtro adicional, que exclui todos os tuples onde o nome do animal de estimação não `Shadow`está . Pode construir tuples a partir de matrizes, filtrar qualquer um dos elementos da tuple, e projetar qualquer combinação dos elementos.

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

- [Começar](sql-query-getting-started.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subqueides](sql-query-subquery.md)