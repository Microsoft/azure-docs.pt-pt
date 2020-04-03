---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento utilizando SQL dinâmico em piscina SQL synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619043"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinâmico em piscina SQL synapse
Incluídos neste artigo estão dicas para soluções de desenvolvimento utilizando SQL dinâmico em piscina SQL.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico de SQL

Ao desenvolver o código de aplicação para o pool SQL, poderá ser necessário utilizar sQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O pool SQL não suporta tipos de dados blob neste momento. 

Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem ambos os tipos de varchar (max) e nvarchar (max). 

Se usou estes tipos no seu código de aplicação para construir grandes cordas, tem de quebrar o código em pedaços e utilizar a declaração do EXEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicas continuarão sujeitas a todas as regras de validação T-SQL.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

