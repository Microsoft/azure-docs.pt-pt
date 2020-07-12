---
title: Funções de data e hora em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar operações de datetime e timetamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246942"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)

As funções de data e hora permitem-lhe executar as operações de datetime e timetamp em Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funções para obter a data e hora

As seguintes funções escalar permitem obter a data e hora utc atuais em duas formas: uma cadeia que está em conformidade com o formato ISO 8601 ou um timetamp numérico cujo valor é a época Unix em milissegundos:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Funções para trabalhar com valores de DataTime

As seguintes funções permitem manipular facilmente os valores do DateTime:

* [DataTimeAdd](sql-query-datetimeadd.md)
* [DataTimediff](sql-query-datetimediff.md)
* [DataTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Passos seguintes

- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
