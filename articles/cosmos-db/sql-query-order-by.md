---
title: CLÁUSULA ORDER BY em Azure Cosmos DB
description: Conheça a cláusula SQL ORDER BY para O Azure Cosmos DB. Use o SQL como uma linguagem de consulta Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188738"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>CLÁUSULA ORDER BY em Azure Cosmos DB

A cláusula OPCIONAL ORDER BY especifica a ordem de triagem dos resultados devolvidos pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão para classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um nome ou pseudónimo de propriedade.  
  
   Várias propriedades podem ser especificadas. Os nomes de propriedade devem ser únicos. A sequência das propriedades do tipo na cláusula ORDER BY define a organização do conjunto de resultados ordenados. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, que a lista ordenada é ordenada pela segunda propriedade, e assim por diante.  
  
   Os nomes de propriedade referidos na cláusula ORDER BY devem corresponder a um imóvel na lista selecionada ou a um imóvel definido na cláusula DE Cobrança especificada na cláusula FROM sem quaisquer ambiguidades.  
  
- `<sort_expression>`  
  
   Especifica uma ou mais propriedades ou expressões para classificar o conjunto de resultados de consulta.  
  
- `<scalar_expression>`  
  
   Consulte a secção de [expressões Scalar](sql-query-scalar-expressions.md) para mais detalhes.  
  
- `ASC | DESC`  
  
   Especifica que os valores na coluna especificada devem ser classificados por ordem ascendente ou descendente. AsC classifica do valor mais baixo para o valor mais alto. O DESC classifica-se de valor mais elevado para valor mais baixo. ASC é a ordem de classificação padrão. Os valores nulos são tratados como os valores mais baixos possíveis.  
  
## <a name="remarks"></a>Observações  
  
   A `ORDER BY` cláusula exige que a política de indexação inclua um índice para os campos que estão a ser classificados. O tempo de execução da consulta Azure Cosmos DB suporta a triagem contra um nome de propriedade e não contra propriedades computadas. A Azure Cosmos `ORDER BY` DB suporta várias propriedades. Para fazer uma consulta com múltiplas propriedades ORDER BY, deve definir um [índice composto](index-policy.md#composite-indexes) sobre os campos que estão sendo classificados.

> [!Note]
> Se as propriedades que estão a ser classificadas podem não estar definidas para alguns documentos e quiser recuperá-las numa consulta ORDER BY, deve incluir explicitamente este caminho no índice. A política de indexação padrão não permitirá a recuperação dos documentos onde a propriedade de classificação é indefinida. [Reveja as consultas de exemplo em documentos com alguns campos desaparecidos.](#documents-with-missing-fields)

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que recupera famílias em ordem ascendente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A seguinte consulta recupera `id`a família por ordem da data de criação do artigo. Item `creationDate` é um número que representa o tempo da *época,* ou tempo decorrido desde 1 de janeiro de 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Além disso, pode encomendar por várias propriedades. Uma consulta que encomenda por várias propriedades requer um [índice composto](index-policy.md#composite-indexes). Considere a consulta seguinte:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Esta consulta recupera a `id` família em ordem ascendente do nome da cidade. Se vários itens tiverem o mesmo nome da cidade, a consulta será ordenada pela `creationDate` ordem descendente.

## <a name="documents-with-missing-fields"></a>Documentos com campos desaparecidos

As consultas `ORDER BY` com que são feitas contra contentores com a política de indexação padrão não devolverão documentos onde a propriedade de classificação é indefinida. Se você gostaria de incluir documentos onde a propriedade de classificação é indefinida, você deve explicitamente incluir esta propriedade na política de indexação.

Por exemplo, aqui está um recipiente com uma política de indexação `"/*"`que não inclui explicitamente quaisquer caminhos além:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se executar uma consulta `lastName` que `Order By` inclua na cláusula, os resultados incluirão apenas documentos que tenham um `lastName` imóvel definido. Não definimos um caminho explícito `lastName` para que quaisquer documentos sem um `lastName` não apareçam nos resultados da consulta.

Aqui está uma consulta `lastName` que se classifica em dois documentos, um dos quais não tem um `lastName` definido:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados incluem apenas `lastName`o documento que tem um definido:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se atualizarmos a política de indexação do contentor `lastName`para incluir explicitamente um caminho para, incluiremos documentos com uma propriedade de tipo indefinida nos resultados da consulta. Deve definir explicitamente o caminho para levar a este valor escalar (e não para além dele). Deve utilizar `?` o personagem na definição do seu caminho na política `lastName` de indexação para garantir que indexa explicitamente a propriedade e nenhum caminho adicional aninhada além dela.

Aqui está uma política de indexação de amostras `lastName` que lhe permite ter documentos com um aparecimento indefinido nos resultados da consulta:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se fizer a mesma consulta novamente, `lastName` os documentos que faltam aparecem primeiro nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados são:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Se modificar a ordem `DESC`de classificação `lastName` para documentos que faltam aparecem em último nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Começar](sql-query-getting-started.md)
- [Políticas de indexação no Azure Cosmos DB](index-policy.md)
- [Cláusula LIMITE OFFSET](sql-query-offset-limit.md)
