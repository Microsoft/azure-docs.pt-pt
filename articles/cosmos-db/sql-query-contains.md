---
title: CONTÉM na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função contém o sistema SQL no Azure Cosmos DB retorna um valor booleano que indica se a primeira expressão de cadeia de caracteres contém a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871538"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de cadeia de caracteres a ser pesquisada.  
  
*str_expr2*  
   É a expressão de cadeia de caracteres a ser localizada.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se "ABC" contém "AB" e se "ABC" contém "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
