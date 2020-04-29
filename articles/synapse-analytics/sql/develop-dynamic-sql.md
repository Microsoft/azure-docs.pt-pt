---
title: Use SQL dinâmico em SQL synapse
description: Dicas para utilizar SQL dinâmico em SQL synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429606"
---
# <a name="dynamic-sql-in-synapse-sql"></a>SQL dinâmico em Synapse SQL
Neste artigo, encontrará dicas para utilizar sQL dinâmico e desenvolver soluções utilizando synapse SQL.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico de SQL

Ao desenvolver o código de aplicação, poderá ser necessário utilizar SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares.

> [!NOTE]
> O pool SQL não suporta tipos de dados blob neste momento. Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem ambos os tipos de varchar (max) e nvarchar (max). Se utilizou estes tipos no seu código de aplicação para construir grandes cordas, tem de quebrar o código em pedaços e utilizar a declaração do EXEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicas continuarão sujeitas a todas as regras de validação T-SQL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](develop-overview.md)
