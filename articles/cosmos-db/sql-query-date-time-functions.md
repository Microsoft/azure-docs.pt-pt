---
title: Funções de data e hora em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar operações de datetime e timetamp.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cfa81b6ec5f10218a70de6b9b55e502d87898194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549184"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções de data e hora permitem-lhe executar as operações de datetime e timetamp em Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funções para obter a data e hora

As seguintes funções escalar permitem-lhe obter a data e hora atuais do UTC em três formas: uma cadeia que está em conformidade com o formato ISO 8601, um carimbo de tempo numérico cujo valor é o número de milissegundos que decorreram desde a época unix, ou carrapatos numéricos cujo valor é o número de 100 tiques nanosegundos que decorreram desde a época unix :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funções para trabalhar com valores de DataTime

As seguintes funções permitem manipular facilmente o DateTime, o relógio e os valores do tiquetaque:

* [DataTimeAdd](sql-query-datetimeadd.md)
* [DataTimediff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DataTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Passos seguintes

- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregate-functions.md)
