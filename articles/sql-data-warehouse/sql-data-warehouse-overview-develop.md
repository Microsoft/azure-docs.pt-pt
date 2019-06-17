---
title: Recursos para desenvolver um armazém de dados no Azure | Documentos da Microsoft
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 613bcb05dab993989a2ae00b71fef95794953ab8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65850740"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de design e técnicas de codificação para o SQL Data Warehouse
Veja estes artigos de desenvolvimento para compreender melhor as principais decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos seguintes realçam os conceitos e as decisões de design para o desenvolvimento de um armazém de dados distribuídos com o SQL Data Warehouse:

* [Ligações][connections]
* [concurrency][concurrency]
* [Transações][transactions]
* [esquemas definidos pelo utilizador][user-defined schemas]
* [distribuição da tabela][table distribution]
* [índices de tabela][table indexes]
* [Partições de tabela][table partitions]
* [CTAS][CTAS]
* [Estatísticas][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Estes artigos realçam técnicas de codificação específicas, sugestões e recomendações para o desenvolvimento do SQL Data Warehouse:

* [Procedimentos armazenados][stored procedures]
* [labels][labels]
* [Modos de exibição][views]
* [Tabelas temporárias][temporary tables]
* [dynamic SQL][dynamic SQL]
* [looping][looping]
* [Agrupar por opções][group by options]
* [atribuição de variáveis][variable assignment]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações de referência, consulte [instruções T-SQL do armazém de dados SQL](sql-data-warehouse-reference-tsql-statements.md).

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
