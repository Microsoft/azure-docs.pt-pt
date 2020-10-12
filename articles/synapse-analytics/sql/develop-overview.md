---
title: Recursos para o desenvolvimento de funcionalidades Sinaapse SQL
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o Sinapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f4c61b43855e4a845956a937d9d9cfe738d6f47a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032950"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para características do SQL da Sinapse no Azure Synapse Analytics
Neste artigo, você encontrará uma lista de recursos para piscina SQL e sql on-demand (pré-visualização) funções de Synapse SQL. Os artigos recomendados dividem-se em duas secções: decisões de conceção chave e técnicas de desenvolvimento e codificação.

O objetivo destes artigos é ajudá-lo a desenvolver a abordagem técnica ideal para os componentes Sinapse SQL dentro da Synapse Analytics.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos abaixo destacam conceitos e decisões de design para o desenvolvimento do SQL da Sinapse:

| Artigo | Conjunto de SQL | SQL a pedido |
| ------- | -------- | ------------- |
| [Ligações](connect-overview.md)                    | Sim | Sim |
| [Classes de recursos e concuência](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sim    | Não |
| [Transações](develop-transactions.md)              | Sim | Não |
| [Esquemas definidos pelo utilizador](develop-user-defined-schemas.md) | Sim | Sim |
| [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sim | Não |
| [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sim | Não |
| [Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sim | Não |
| [Estatísticas](develop-tables-statistics.md)            | Sim | Sim |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sim | Não |
| [Tabelas externas](develop-tables-external-tables.md) | Sim | Sim |
| [CETAS](develop-tables-cetas.md)                     | Sim | Sim |


## <a name="recommendations"></a>Recomendações

Abaixo encontrará artigos essenciais que enfatizam técnicas específicas de codificação, dicas e recomendações para o desenvolvimento:

| Artigo | Conjunto de SQL | SQL a pedido |
| ------- | -------- | ------------- |
| [Procedimentos armazenados](develop-stored-procedures.md)  | Sim                | Não                      |
| [Etiquetas](develop-label.md)                           | Sim                | Não                      |
| [Vistas](develop-views.md)                             | Sim                | Sim                     |
| [Tabelas temporárias](develop-tables-temporary.md)       | Sim                | Sim                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Sim                | Sim                     |
| [Executar ciclos](develop-loops.md)                         | Sim                | Sim                     |
| [Agrupar por opções](develop-group-by-options.md)       | Sim                | Não                      |
| [Atribuição de variáveis](develop-variable-assignment.md) | Sim                | Sim                     |

## <a name="next-steps"></a>Próximos passos
Para obter mais informações de referência, consulte [as declarações T-SQL da piscina SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

