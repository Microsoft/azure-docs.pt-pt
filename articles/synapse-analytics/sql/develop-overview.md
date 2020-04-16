---
title: Recursos para desenvolver funcionalidades SYnapse SQL
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429021"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para funcionalidades SYnapse SQL em Azure Synapse Analytics
Neste artigo, você encontrará uma lista de recursos para o pool SQL e funções sQL on-demand (pré-visualização) da Synapse SQL. Os artigos recomendados são divididos em duas secções: Decisões-chave de design e técnicas de desenvolvimento e codificação.

O objetivo destes artigos é ajudá-lo a desenvolver a abordagem técnica ideal para os componentes SQL synapse dentro da Synapse Analytics.

## <a name="key-design-decisions"></a>Decisões-chave de design
Os artigos abaixo destacam conceitos e decisões de design para o desenvolvimento synapse SQL:

|                                                          |   Piscina SQL   | SQL a pedido |
| -----------------------------------------------------    | ---- | ---- |
| [Ligações](connect-overview.md)                    | Sim | Sim |
| [Classes de recursos e conmoeda](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sim    | Não |
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

|                                            | Piscina SQL | SQL a pedido |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Procedimentos armazenados](develop-stored-procedures.md)  | Sim                | Não                      |
| [Etiquetas](develop-label.md)                           | Sim                | Não                      |
| [Vistas](develop-views.md)                             | Sim                | Sim                     |
| [Tabelas temporárias](develop-tables-temporary.md)       | Sim                | Sim                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Sim                | Sim                     |
| [Executar ciclos](develop-loops.md)                         | Sim                | Sim                     |
| [Agrupar por opções](develop-group-by-options.md)       | Sim                | Não                      |
| [Atribuição de variáveis](develop-variable-assignment.md) | Sim                | Sim                     |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações de referência, consulte [as declarações t-SQL do pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

