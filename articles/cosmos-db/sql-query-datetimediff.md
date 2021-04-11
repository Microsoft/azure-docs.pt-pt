---
title: DataTimeDiff em linguagem de consulta DB do Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimeDiff em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595649"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Devolve a contagem (como valor inteiro assinado) dos limites especificados DateTimePart cruzados entre o *StartDate* especificado e o *EndDate*.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
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

*Fim da Date*  
   UtC data e tempo ISO 8601 valor de cadeia no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Devolve um valor inteiro assinado.

## <a name="remarks"></a>Observações

DateTimeDiff regressará `undefined` pelas seguintes razões:

- O valor DateTimePart especificado é inválido
- O StartDate ou EndDate não é um Iso 8601 DateTime válido

DateTimeDiff irá sempre devolver um valor inteiro assinado e é uma medição do número de limites DateTimePart cruzados, não medindo o intervalo de tempo.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir calcula o número de fronteiras diurnas cruzadas entre `2020-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

O exemplo a seguir calcula o número de limites do ano cruzado entre `2028-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

O exemplo a seguir calcula o número de fronteiras horárias cruzadas entre `2020-01-01T01:00:00.1234527Z` e `2020-01-01T01:59:59.1234567Z` . Apesar destes valores de DateTime estarem a mais de 0,99 horas de intervalo, `DateTimeDiff` o retorno é 0 porque não foram ultrapassados os limites de horas.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
