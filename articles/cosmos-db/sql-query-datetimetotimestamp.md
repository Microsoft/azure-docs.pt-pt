---
title: DataTimeToTimestamp em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimeToTimestamp em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608827"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DataTimeToTimestamp (Azure Cosmos DB)

Converte o Tempo de Data especificado para um tempotando.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumentos

*DateTime*  
   Utc data e hora ISO 8601 valor de cadeia no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` onde:
  
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

## <a name="return-types"></a>Tipos de retorno

Devolve um valor numérico assinado, o número atual de milissegundos que decorreram desde a época Unix, ou seja, o número de milissegundos que decorreram desde as 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

DataTimeToTimestamp regressará `undefined` se o valor do DateTime especificado for inválido

## <a name="examples"></a>Exemplos
  
O exemplo a seguir converte o DateTime num timetamp:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Eis outro exemplo:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
