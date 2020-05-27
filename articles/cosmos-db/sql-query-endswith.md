---
title: Termina Com a linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema ENDSWITH SQL em Azure Cosmos DB para devolver um Boolean indicando se a primeira expressão de corda termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0cd927af50eca04aa8162d9d8f292077d9e4165c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844969"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Devolve uma Boolean indicando se a primeira expressão de corda termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cordas.  
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o final de *str_expr1*.

*bool_expr* Valor opcional para ignorar caso. Quando for em vigor, a ENDSWITH fará uma pesquisa insensível em casos. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
O exemplo seguinte verifica se a cadeia "abc" termina com "b" e "bC".  
  
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

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

O consumo de RU de EndsWith aumentará à medida que a cardinalidade do imóvel na função do sistema aumenta. Por outras palavras, se estiver a verificar se um valor de propriedade termina com uma certa cadeia, a taxa de consulta RU dependerá do número de valores possíveis para esse imóvel.

Por exemplo, considere duas propriedades: cidade e país. A cardinalidade da cidade é de 5.000 e a cardinalidade do país é de 200. Aqui estão duas consultas exemplo:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

A primeira consulta provavelmente usará mais RUs do que a segunda consulta porque a cardinalidade da cidade é maior do que o país.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
