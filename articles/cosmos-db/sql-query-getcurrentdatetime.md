---
title: GetCurrentDateTime em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL GetCurrentDateTime em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587309"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
|Z|Designador UTC (Tempo Universal Coordenado)|
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observações

GetCurrentDateTime é uma função não determinística. O resultado devolvido é UTC. Precisão é de 7 dígitos, com uma precisão de 100 nanosegundos.

> [!NOTE]
> Esta função do sistema não utilizará o índice. Se precisar de comparar valores com o tempo atual, obtenha o tempo atual antes da execução da consulta e use esse valor de cadeia constante na `WHERE` cláusula.

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
