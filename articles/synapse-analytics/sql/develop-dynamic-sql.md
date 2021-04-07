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
ms.openlocfilehash: 86d5028a09a805142f7632f93530f8a54965d82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675039"
---
# <a name="dynamic-sql-in-synapse-sql"></a>SQL dinâmico em SQL de Sinapse

Neste artigo, encontrará dicas para utilizar SQL dinâmico e desenvolver soluções utilizando o Sinapse SQL.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico do SQL

Ao desenvolver o código de aplicação, poderá ser necessário utilizar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares.

> [!NOTE]
> O pool de SQL dedicado não suporta tipos de dados blob neste momento. Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem tanto os tipos de varchar(máx) como os nvarchar (max). Se utilizou estes tipos no seu código de aplicação para construir cadeias grandes, tem de descodificá-lo em pedaços e utilizar a declaração DOEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode [usá sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?view=azure-sqldw-latest&preserve-view=true) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicos continuarão sujeitas a todas as regras de validação T-SQL.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)
