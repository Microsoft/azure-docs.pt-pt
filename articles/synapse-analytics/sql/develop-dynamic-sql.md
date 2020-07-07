---
title: Use SQL dinâmico em SQL sinapse
description: Dicas para utilizar SQL dinâmico em Sinaapse SQL.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429606"
---
# <a name="dynamic-sql-in-synapse-sql"></a>SQL dinâmico em SQL de Sinapse
Neste artigo, encontrará dicas para utilizar SQL dinâmico e desenvolver soluções utilizando o Sinapse SQL.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico do SQL

Ao desenvolver o código de aplicação, poderá ser necessário utilizar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares.

> [!NOTE]
> O pool SQL não suporta tipos de dados blob neste momento. Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem tanto os tipos de varchar(máx) como os nvarchar (max). Se utilizou estes tipos no seu código de aplicação para construir cadeias grandes, tem de descodificá-lo em pedaços e utilizar a declaração DOEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode [usásp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicos continuarão sujeitas a todas as regras de validação T-SQL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
