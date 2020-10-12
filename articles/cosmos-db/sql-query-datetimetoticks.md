---
title: DataTimeToTicks em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimeToTicks em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605134"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

Converte o Tempo de Data especificado para carraças. Um único carrapato representa cem nanossegundos ou um décimo-milionésimo de segundo. 

## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTime*  
   UtC data e tempo ISO 8601 valor de cadeia no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Devolve um valor numérico assinado, o número atual de carrapatos de 100 nanosegundos que decorreram desde a época Unix. Por outras palavras, dateTimeToTicks devolve o número de tiques de 100-nanosegundos que decorreram desde as 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

DataTimeDateTimeToTicks regressará `undefined` se o DateTime não for um Tempo de Data ISO 8601 válido

Esta função do sistema não utilizará o índice.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que devolve o número de carrapatos:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Aqui está um exemplo que devolve o número de carrapatos sem especificar o número de segundos fracionados:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
