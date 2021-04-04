---
title: ENCOMENDA POR cláusula em Azure Cosmos DB
description: Saiba mais sobre SQL ORDER BY cláusula para Azure Cosmos DB. Use o SQL como uma linguagem de consulta Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338307"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ENCOMENDA POR cláusula em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A cláusula opcional `ORDER BY` especifica a ordem de triagem dos resultados devolvidos pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão para classificar o conjunto de resultados de consulta. Uma coluna tipo pode ser especificada como um nome ou pseudónimo de propriedade.  
  
   Várias propriedades podem ser especificadas. Os nomes das propriedades devem ser únicos. A sequência das propriedades do tipo na `ORDER BY` cláusula define a organização do conjunto de resultados classificados. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, a lista ordenada é ordenada pela segunda propriedade, e assim por diante.  
  
   Os nomes de propriedade referenciados na `ORDER BY` cláusula devem corresponder a um imóvel na lista selecionada ou a um imóvel definido na coleção especificado na cláusula sem qualquer `FROM` ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica uma ou mais propriedades ou expressões para classificar o conjunto de resultados de consulta.  
  
- `<scalar_expression>`  
  
   Consulte a secção [de expressões Scalar](sql-query-scalar-expressions.md) para obter mais detalhes.  
  
- `ASC | DESC`  
  
   Especifica que os valores na coluna especificada devem ser classificados por ordem ascendente ou descendente. `ASC` classifica do valor mais baixo para o valor mais alto. `DESC` classifica do valor mais alto para o valor mais baixo. `ASC` é a ordem de classificação padrão. Os valores nulos são tratados como os valores mais baixos possíveis.  
  
## <a name="remarks"></a>Observações  
  
   A `ORDER BY` cláusula exige que a política de indexação inclua um índice para os campos que estão a ser classificados. O tempo de verificação da consulta Azure Cosmos DB suporta a triagem contra um nome de propriedade e não contra propriedades computadas. A Azure Cosmos DB suporta múltiplas `ORDER BY` propriedades. Para executar uma consulta com várias propriedades ORDER BY, deve definir um [índice composto](index-policy.md#composite-indexes) nos campos que estão a ser classificados.

> [!Note]
> Se as propriedades que estão a ser classificadas podem não estar indefinidas para alguns documentos e pretender recuperá-las numa consulta ORDER BY, deve incluir explicitamente este caminho no índice. A política de indexação predefinida não permitirá a recuperação dos documentos onde a propriedade do tipo é indefinida. [Reveja as consultas de exemplo sobre documentos com alguns campos em falta.](#documents-with-missing-fields)

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que recupera famílias em ordem crescente do nome da cidade residente:

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

A seguinte consulta recupera a família `id` s por ordem da data de criação do item. Item `creationDate` é um número que representa o tempo da *época,* ou tempo decorrido desde 1 de janeiro de 1970 em segundos.

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

Esta consulta recupera a família `id` em ordem crescente do nome da cidade. Se vários itens tiverem o mesmo nome da cidade, a consulta encomendará pela `creationDate` ordem descendente.

## <a name="documents-with-missing-fields"></a>Documentos com campos em falta

As consultas com `ORDER BY` as quais são executadas contra contentores com a política de indexação predefinida não devolverão documentos onde a propriedade do tipo é indefinida. Se você gostaria de incluir documentos onde a propriedade de classificação é indefinida, você deve explicitamente incluir esta propriedade na política de indexação.

Por exemplo, aqui está um recipiente com uma política de indexação que não inclui explicitamente quaisquer caminhos além `"/*"` de:

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

Se executar uma consulta que inclua `lastName` na `Order By` cláusula, os resultados apenas incluirão documentos que tenham um `lastName` imóvel definido. Não definimos um caminho explicito incluído para `lastName` que quaisquer documentos sem um `lastName` não apareçam nos resultados da consulta.

Aqui está uma consulta que classifica por `lastName` dois documentos, um dos quais não tem um `lastName` definido:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados incluem apenas o documento que tem um `lastName` definido:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se atualizarmos a política de indexação do contentor para incluir explicitamente um caminho `lastName` para, iremos incluir documentos com uma propriedade de classificação indefinida nos resultados da consulta. Deve definir explicitamente o caminho para levar a este valor escalar (e não além dele). Deve utilizar o `?` carácter na definição do seu percurso na política de indexação para garantir que indexa explicitamente a propriedade `lastName` e não há caminhos aninhados adicionais para além dela. Se a sua `Order By` consulta utilizar um índice [composto,](index-policy.md#composite-indexes)os resultados incluirão sempre documentos com uma propriedade de classificação indefinida nos resultados da consulta.

Aqui está uma política de indexação de amostras que permite ter documentos com uma aparecerem indefinidamente `lastName` nos resultados da consulta:

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

Se voltar a fazer a mesma consulta, os documentos que faltam `lastName` aparecem primeiro nos resultados da consulta:

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

Se modificar a ordem de classificação `DESC` para, os documentos em falta `lastName` aparecem em último lugar nos resultados da consulta:

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

> [!Note]
> Apenas a versão .NET SDK 3.4.0 ou posteriormente suporta ORDER BY com tipos mistos. Portanto, se quiser ordenar por uma combinação de valores indefinidos e definidos, deve utilizar esta versão (ou mais tarde).

Não é possível controlar a ordem que diferentes tipos aparecem nos resultados. No exemplo acima, mostrámos como os valores indefinidos eram classificados antes dos valores das cordas. Se, por exemplo, quisesse mais controlo sobre a ordem de valores indefinidos, poderia atribuir a quaisquer propriedades indefinidas um valor de cadeia de "aaaaaaaa" ou "zzzzzzzz" para garantir que eram em primeiro ou último lugar.

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Políticas de indexação no Azure Cosmos DB](index-policy.md)
- [CLÁUSULA OFFSET LIMIT](sql-query-offset-limit.md)
