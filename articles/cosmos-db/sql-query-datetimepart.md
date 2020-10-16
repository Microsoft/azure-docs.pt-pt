---
title: DataTimePart em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimePart em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227388"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)

Devolve o valor da DataTimePart especificada entre o Tempo de Data especificado.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DataTimePart*  
   A parte da data para a qual o DateTimePart devolverá o valor. Esta tabela lista todos os argumentos válidos dateTimePart:

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

*DateTime*  
   UtC data e tempo ISO 8601 valor de cadeia no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Devolve um valor inteiro positivo.

## <a name="remarks"></a>Observações

DateTimePart regressará `undefined` pelas seguintes razões:

- O valor DateTimePart especificado é inválido
- O DateTime não é um Tempo de Data ISO 8601 válido

Esta função do sistema não utilizará o índice.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que devolve o valor inteiro do mês:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Aqui está um exemplo que devolve o número de microsegundos:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
