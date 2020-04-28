---
title: PI em linguagem de consulta de Azure Cosmos DB
description: Conheça a função do sistema SQL PI em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349661"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Devolve o valor constante de PI.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve `PI`o valor de .  
  
```sql
SELECT PI() AS pi 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
