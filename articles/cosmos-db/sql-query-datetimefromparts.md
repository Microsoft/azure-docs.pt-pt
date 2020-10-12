---
title: DataTimeFromParts em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL DataTimeFromParts em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86262059"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DataTimeFromParts (Azure Cosmos DB)

Devolve uma sequência de valor DateTime construído a partir de valores de entrada.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumentos
  
*número Ano* Valor inteiro para o ano no formato `YYYY`

*númeroSEso*  
   Valor inteiro para o mês no formato `MM`

*número Dia*  
   Valor inteiro para o dia no formato `DD`

*númeroHour* (opcional) Valor inteiro para a hora no formato `hh`

*númeroMinute* (opcional) Valor inteiro para o minuto no formato `mm`

*númeroSegundo* (opcional) Valor inteiro para o segundo no formato `ss`

*númeroOfFractionsOfSecond* (opcional) Valor inteiro para a fração de um segundo no formato `.fffffff`

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
  |Z|Designador UTC (Tempo Universal Coordenado)||
  
 Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observações

Se os números inteiros especificados criarem uma DataTime inválida, dataTimeFromParts regressará `undefined` .

Se um argumento opcional não for especificado, o seu valor será 0.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que só inclui argumentos necessários para construir uma DataTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Aqui está outro exemplo que também usa alguns argumentos opcionais para construir uma DataTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Aqui está outro exemplo que também usa todos os argumentos opcionais para construir uma DataTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
