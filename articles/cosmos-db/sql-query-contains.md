---
title: Contém em Azure Cosmos DB linguagem de consulta
description: Saiba como o sistema DE SQL CONTÉM funciona no Azure Cosmos DB devolve um Boolean indicando se a primeira expressão de corda contém a segunda
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339177"
---
# <a name="contains-azure-cosmos-db"></a>CONTÉM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Devolve um Boolean indicando se a primeira expressão de corda contém a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda para encontrar.  

*bool_expr* Valor opcional para ignorar o caso. Quando definido como verdadeiro, o CONTAINS fará uma pesquisa insensível a casos. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se "abc" contém "ab" e se "abc" contém "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

O consumo de RU de Contém aumentará à medida que a cardinalidade da propriedade no sistema aumenta. Por outras palavras, se estiver a verificar se um valor de propriedade contém uma determinada cadeia, a taxa DE CONSULTA RU dependerá do número de valores possíveis para essa propriedade.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade da cidade é de 5.000 e a cardinalidade do país é de 200. Aqui estão duas consultas de exemplo:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta porque o cardinalício da cidade é maior do que o país.

Se o tamanho da propriedade em Contém for superior a 1 KB para alguns documentos, o motor de consulta terá de carregar esses documentos. Neste caso, o motor de consulta não será capaz de avaliar totalmente contém com um índice. A taxa RU para conter será elevada se tiver um grande número de documentos com tamanhos de propriedade superior a 1 KB.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
