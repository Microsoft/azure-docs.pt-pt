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
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377335"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para características do SQL da Sinapse no Azure Synapse Analytics
Neste artigo, você encontrará uma lista de recursos para piscina SQL dedicada e funções de piscina SQL sem servidor do Synapse SQL. Os artigos recomendados dividem-se em duas secções: decisões de conceção chave e técnicas de desenvolvimento e codificação.

O objetivo destes artigos é ajudá-lo a desenvolver a abordagem técnica ideal para os componentes Sinapse SQL dentro do Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos abaixo destacam conceitos e decisões de design para o desenvolvimento do SQL da Sinapse:

| Artigo | piscina de SQL dedicada | conjunto de SQL sem servidor |
| ------- | -------- | ------------- |
| [Ligações](connect-overview.md)                    | Yes | Yes |
| [Classes de recursos e concuência](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | No |
| [Transações](develop-transactions.md)              | Yes | No |
| [Esquemas definidos pelo utilizador](develop-user-defined-schemas.md) | Yes | Yes |
| [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | No |
| [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | No |
| [Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | No |
| [Estatísticas](develop-tables-statistics.md)            | Yes | Yes |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | No |
| [Tabelas externas](develop-tables-external-tables.md) | Yes | Yes |
| [CETAS](develop-tables-cetas.md)                     | Yes | Yes |


## <a name="recommendations"></a>Recomendações

Abaixo encontrará artigos essenciais que enfatizam técnicas específicas de codificação, dicas e recomendações para o desenvolvimento:

| Artigo | piscina de SQL dedicada | conjunto de SQL sem servidor |
| ------- | -------- | ------------- |
| [Procedimentos armazenados](develop-stored-procedures.md)  | Yes                | Yes                      |
| [Etiquetas](develop-label.md)                           | Yes                | No                      |
| [Vistas](develop-views.md)                             | Yes                | Yes                     |
| [Tabelas temporárias](develop-tables-temporary.md)       | Yes                | Yes                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Yes                | Yes                     |
| [Executar ciclos](develop-loops.md)                         | Yes                | Yes                     |
| [Agrupar por opções](develop-group-by-options.md)       | Yes                | No                      |
| [Atribuição de variáveis](develop-variable-assignment.md) | Yes                | Sim                     |

## <a name="next-steps"></a>Próximos passos
Para obter mais informações de referência, consulte [as declarações T-SQL da piscina SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

