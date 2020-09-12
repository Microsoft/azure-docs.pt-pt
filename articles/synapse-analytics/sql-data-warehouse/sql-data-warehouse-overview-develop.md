---
title: Recursos para o desenvolvimento de uma piscina Sinapse SQL em Azure Synapse Analytics
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para a Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 95f712f196c37650b52220c9e34f6cb6b50bff23
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460614"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para uma piscina Sinaapse SQL em Azure Synapse Analytics 
 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para uma piscina SQL em Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os seguintes artigos destacam conceitos e decisões de design para o desenvolvimento de um armazém de dados distribuído utilizando a capacidade de piscina SQL em Azure Synapse:

* [conexões](../sql/connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo utilizador](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição de tabelas](sql-data-warehouse-tables-distribute.md)
* [índices de tabela](sql-data-warehouse-tables-index.md)
* [divisórias de mesa](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [estatísticas](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações de desenvolvimento e técnicas de codificação
Os seguintes artigos apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de uma piscina SQL:

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [rótulos](sql-data-warehouse-develop-label.md)
* [Modos de exibição](performance-tuning-materialized-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [looping](sql-data-warehouse-develop-loops.md)
* [grupo por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Próximos passos
Para obter mais informações de referência, consulte [as declarações T-SQL](sql-data-warehouse-reference-tsql-statements.md).
