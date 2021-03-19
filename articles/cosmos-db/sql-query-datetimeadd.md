---
title: DataTimeAdd em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimeAdd em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597662"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Devolução do valor da cadeia DateTime resultante da adição de um valor de número especificado (como um número inteiro assinado) a uma cadeia de DateTime especificada  
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DataTimePart*  
   A parte da data à qual o DateTimeAdd adiciona um número inteiro. Esta tabela lista todos os argumentos válidos dateTimePart:

| DateTimePart | abreviaturas        |
| ------------ | -------------------- |
| Anual         | "ano", "yyyy", "yy" |
| Mensal        | "mês", "mm", "m"   |
| Dia          | "dia", "dd", "d"     |
| Hora         | "hora", "hh"         |
| Minuto       | "minuto", "mi", "n"  |
| Second       | "segundo", "ss", "s"  |
| Milissegundo  | "milissegundo", "ms"  |
| Microsegundo  | "microsegundo", "mcs" |
| Nanossegundo   | "nanossegundo", "ns"   |

*numeric_expr*  
   É um valor inteiro assinado que será adicionado ao DateTimePart da DataTime

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
|Z|Designador UTC (Tempo Universal Coordenado)|
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Tipos de retorno

Devolve uma data e hora UTC valor de cadeia ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` onde:
  
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

## <a name="remarks"></a>Observações

DateTimeAdd regressará `undefined` pelas seguintes razões:

- O valor DateTimePart especificado é inválido
- O numeric_expr especificado não é um número inteiro válido
- O DateTime no argumento ou resultado não é um Tempo de Data ISO 8601 válido.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir adiciona 1 mês à DataTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

O exemplo a seguir subtrai 2 horas a partir do Tempo de Data: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
