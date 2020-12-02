---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento utilizando SQL dinâmico para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 52bc7bdc63f754d52bf4a69097c1dd309a6dc3ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462773"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>SQL dinâmico para piscinas SQL dedicadas em Azure Synapse Analytics

Incluído neste artigo estão dicas para soluções de desenvolvimento usando SQL dinâmico em piscinas SQL dedicadas.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico do SQL

Ao desenvolver código de aplicação para piscinas SQL dedicadas, poderá ser necessário utilizar SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. Piscinas SQL dedicadas não suportam tipos de dados blob neste momento.

Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem tanto os tipos de varchar(máx) como os nvarchar (max).

Se utilizou estes tipos no seu código de aplicação para construir cordas grandes, tem de dividir o código em pedaços e utilizar a declaração DOEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode [usá sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicos continuarão sujeitas a todas as regras de validação T-SQL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
