---
title: TimestampToDateTime in Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL TimestampToDateTime em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: af5e0eeb808835fd4f36a0eda79618f831248ad9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340775"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimetampToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte o valor do tempotamp especificado para uma DataTime.
  
## <a name="syntax"></a>Sintaxe
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Argumentos

*Timestamp*  

Um valor numérico assinado, o número atual de milissegundos que decorreram desde a época Unix. Ou seja, o número de milissegundos que decorreram desde as 00:00 de quinta-feira, 1 de janeiro de 1970.

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

TimetampToDateTime regressará `undefined` se o valor da hora especificada for inválido.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir converte a tabela de tempos para uma DataTime:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
