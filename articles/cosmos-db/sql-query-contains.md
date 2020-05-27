---
title: Contém na linguagem de consulta do Azure Cosmos DB
description: Saiba como o sistema CONTAINS SQL funciona em Azure Cosmos DB devolve uma Boolean indicando se a primeira expressão de corda contém a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a08fe47122d7e9ddd1c9038bb5f15ebbb0be30fa
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848979"
---
# <a name="contains-azure-cosmos-db"></a>CONTÉM (Azure Cosmos DB)

 Devolve uma Boolean indicando se a primeira expressão de corda contém a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda para encontrar.  

*bool_expr* Valor opcional para ignorar caso. Quando for em vigor, a CONTAINS fará uma pesquisa insensível em casos. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  Verifica-se se "abc" contém "ab" e se "abc" contém "A".  
  
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

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

O consumo de Contéms da RU aumentará à medida que a cardinalidade da propriedade na função do sistema aumenta. Por outras palavras, se estiver a verificar se um valor de propriedade contém uma certa cadeia, a taxa de consulta RU dependerá do número de valores possíveis para esse imóvel.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade da cidade é de 5.000 e a cardinalidade do país é de 200. Aqui estão duas consultas exemplo:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta porque a cardinalidade da cidade é maior do que o país.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
