---
title: Termina em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema ENDSWITH SQL em Azure Cosmos DB para devolver um Boolean indicando se a primeira expressão de corda termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338837"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Devolve um Boolean indicando se a primeira expressão de corda termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de corda.  
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o fim de *str_expr1*.

*bool_expr* Valor opcional para ignorar o caso. Quando definido como verdadeiro, ENDSWITH fará uma pesquisa caso-insensível. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir verifica se a cadeia "abc" termina com "b" e "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

O consumo de RU de EndsWith aumentará à medida que a cardinalidade do imóvel no sistema aumenta. Por outras palavras, se estiver a verificar se um valor de propriedade termina com uma determinada cadeia, a taxa DE CONSULTA RU dependerá do número de valores possíveis para esse imóvel.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade da cidade é de 5.000 e a cardinalidade do país é de 200. Aqui estão duas consultas de exemplo:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta porque o cardinalício da cidade é maior do que o país.

Se o tamanho da propriedade em EndsWith for superior a 1 KB para alguns documentos, o motor de consulta terá de carregar esses documentos. Neste caso, o motor de consulta não será capaz de avaliar totalmente EndsWith com um índice. A taxa RU para EndsWith será elevada se tiver um grande número de documentos com tamanhos de propriedade superiores a 1 KB.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
