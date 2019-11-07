---
title: Recursos para o desenvolvimento de um data warehouse no Azure
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645621"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de design e técnicas de codificação para SQL Data Warehouse
Dê uma olhada nesses artigos de desenvolvimento para entender melhor as principais decisões de design, recomendações e técnicas de codificação para SQL Data Warehouse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam os conceitos e as decisões de design para o desenvolvimento de um data warehouse distribuído usando SQL Data Warehouse:

* [conexões][connections]
* [corrente][concurrency]
* [transações][transactions]
* [esquemas definidos pelo usuário][user-defined schemas]
* [distribuição de tabela][table distribution]
* [índices de tabela][table indexes]
* [partições de tabela][table partitions]
* [CTAS][CTAS]
* [estatísticas][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações de desenvolvimento e técnicas de codificação
Estes artigos destacam técnicas de codificação, dicas e recomendações específicas para o desenvolvimento de seus SQL Data Warehouse:

* [procedimentos armazenados][stored procedures]
* [las][labels]
* [Modos de exibição][views]
* [tabelas temporárias][temporary tables]
* [SQL dinâmico][dynamic SQL]
* [loop][looping]
* [Agrupar por opções][group by options]
* [atribuição de variável][variable assignment]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações de referência, consulte [SQL data warehouse instruções T-SQL](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
