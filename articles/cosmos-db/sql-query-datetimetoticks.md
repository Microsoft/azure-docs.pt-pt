---
title: DataTimeToTicks em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL DateTimeToTicks em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a6fcff691ee5278ed0a9e1c132f8ecebbee9431c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227244"
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

Devolve um valor inteiro positivo.

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

## <a name="next-steps"></a>Próximos passos

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
