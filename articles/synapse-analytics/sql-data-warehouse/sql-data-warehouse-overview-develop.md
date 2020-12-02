---
title: Recursos para o desenvolvimento de uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para uma piscina DE SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453678"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics 

 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para uma piscina DE SQL dedicada (anteriormente SQL DW) em Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design

Os seguintes artigos destacam conceitos e decisões de design para o desenvolvimento de um armazém de dados distribuído utilizando a capacidade dedicada de piscina SQL (anteriormente SQL DW) em Azure Synapse:

* [conexões](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo utilizador](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição de tabelas](sql-data-warehouse-tables-distribute.md)
* [índices de tabela](sql-data-warehouse-tables-index.md)
* [divisórias de mesa](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [estatísticas](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações de desenvolvimento e técnicas de codificação

Os seguintes artigos apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de uma piscina SQL dedicada (anteriormente SQL DW):

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [rótulos](sql-data-warehouse-develop-label.md)
* [Modos de exibição](performance-tuning-materialized-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [looping](sql-data-warehouse-develop-loops.md)
* [grupo por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações de referência, consulte [as declarações T-SQL](sql-data-warehouse-reference-tsql-statements.md).
