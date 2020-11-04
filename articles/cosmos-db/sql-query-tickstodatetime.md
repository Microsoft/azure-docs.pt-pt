---
title: TicksToDateTime em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL TicksToDateTime em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f40286a39694307ac43ecd60f6861d509f760990
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340805"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte o valor de carraças especificado para uma DataTime.
  
## <a name="syntax"></a>Sintaxe
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argumentos

*Carrapatos*  

Um valor numérico assinado, o número atual de 100 carrapatos de nanossegundo que decorreram desde a época unix. Ou seja, é o número de 100 tiques nanossegudos que decorreram desde as 00:00 de quinta-feira, 1 de janeiro de 1970.

## <a name="return-types"></a>Tipos de retorno

Devolve a data e hora da UTC o valor da corda ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` onde:
  
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

O TicksToDateTime regressará `undefined` se o valor dos carrapatos especificado for inválido.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir converte os tiques para uma DataTime:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
