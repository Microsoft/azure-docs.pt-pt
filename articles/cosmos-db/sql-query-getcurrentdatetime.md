---
title: GetCurrentDateTime em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL GetCurrentDateTime em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4db2ec7bbd05d982af57c05fd46ef7da7c682e15
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258810"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)

Devolve a data e hora atuais da UTC (Hora Universal Coordenada) como uma cadeia ISO 8601.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Tipos de retorno
  
  Devolve o valor atual da cadeia DE UTC 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` onde:
  
  |Formato|Descrição|
  |-|-|
  |YYYY|quatro dígitos ano|
  |MM|mês de dois dígitos (01 = janeiro, etc.)|
  |DD|dia de dois dígitos do mês (01 a 31)|
  |T|significador para elementos de início de tempo|
  |hh|hora de dois dígitos (00 a 23)|
  |mm|minutos de dois dígitos (00 a 59)|
  |ss|segundos de dois dígitos (00 a 59)|
  |.fffffff|sete dígitos de segundos fraccionativos|
  |Z|Designador UTC (Tempo Universal Coordenado)||
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observações

  GetCurrentDateTime é uma função não determinística.
  
  O resultado devolvido é UTC.

  Precisão é de 7 dígitos, com uma precisão de 100 nanosegundos.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter a hora de data utc atual usando a função GetCurrentDateTime() incorporada.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Aqui está um conjunto de resultados de exemplo.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
