---
title: Use SQL dinâmico em SQL sinapse
description: Dicas para utilizar SQL dinâmico em Sinaapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 4a4ce92278d5f6704fec4a5b9d30b44ddf89b0ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033443"
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

Se a corda for curta, pode [usá sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicos continuarão sujeitas a todas as regras de validação T-SQL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
