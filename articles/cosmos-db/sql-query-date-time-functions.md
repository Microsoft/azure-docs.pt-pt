---
title: Funções de data e hora em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar operações de datetime e timetamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224956"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)

As funções de data e hora permitem-lhe executar as operações de datetime e timetamp em Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funções para obter a data e hora

As seguintes funções escalar permitem obter a data e hora utc atuais em duas formas: uma cadeia que está em conformidade com o formato ISO 8601 ou um timetamp numérico cujo valor é a época Unix em milissegundos:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funções para trabalhar com valores de DataTime

As seguintes funções permitem manipular facilmente os valores do DateTime:

* [DataTimeAdd](sql-query-datetimeadd.md)
* [DataTimediff](sql-query-datetimediff.md)
* [DataTimeFromParts](sql-query-datetimefromparts.md)
* [DataTimePart](sql-query-datetimepart.md)
* [DataTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>Próximos passos

- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
