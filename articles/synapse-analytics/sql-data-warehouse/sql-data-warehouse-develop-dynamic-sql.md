---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento utilizando SQL dinâmico na piscina Sinaapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bce79b8e18b3ec6f1fd139af280086281bbdda98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213470"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinâmico na piscina Sinaapse SQL

Incluído neste artigo são dicas para soluções de desenvolvimento usando SQL dinâmico em piscina SQL.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico do SQL

Ao desenvolver o código de aplicação para piscina SQL, poderá ser necessário utilizar sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O pool SQL não suporta tipos de dados blob neste momento.

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
