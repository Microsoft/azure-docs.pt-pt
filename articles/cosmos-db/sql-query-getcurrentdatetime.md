---
title: GetCurrentDateTime em linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentDateTime em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303907"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Devolve a data e hora atual utc (Hora Universal Coordenada) como uma cadeia ISO 8601.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve o valor atual da data e hora UTC `YYYY-MM-DDThh:mm:ss.fffffffZ` ISO 8601 no formato onde:
  
  |||
  |-|-|
  |Yyyy|quatro dígitos ano|
  |MM|mês de dois dígitos (01 = janeiro, etc.)|
  |DD|dois dígitos do mês (01 a 31)|
  |T|significador para elementos de início de tempo|
  |hh|hora de dois dígitos (00 a 23)|
  |mm|minutos de dois dígitos (00 a 59)|
  |ss|segundos de dois dígitos (00 a 59)|
  |.fffffff|sete dígitos fracionários segundos|
  |Z|Designador UTC (Tempo Universal Coordenado)||
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observações

  GetCurrentDateTime é uma função não determinística. 
  
  O resultado devolvido é UTC.

  Precisão é de 7 dígitos, com uma precisão de 100 nanosegundos.

## <a name="examples"></a>Exemplos
  
  O exemplo seguinte mostra como obter o atual tempo de data UTC usando a função GetCurrentDateTime() incorporada.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Aqui está um exemplo de resultadodefinido.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
