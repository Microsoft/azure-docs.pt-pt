---
title: Usando SQL dinâmico
description: Dicas para utilizar o SQL dinâmico no Azure SQL Data Warehouse para o desenvolvimento de soluções.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350560"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no Armazém de Dados SQL
Dicas para utilizar o SQL dinâmico no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="dynamic-sql-example"></a>Exemplo dinâmico de SQL

Ao desenvolver o código de aplicação para o SQL Data Warehouse, poderá ser necessário utilizar sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O SQL Data Warehouse não suporta tipos de dados blob neste momento. Não suportar tipos de dados blob pode limitar o tamanho das suas cordas, uma vez que os tipos de dados blob incluem ambos os tipos de varchar (max) e nvarchar (max). Se utilizou estes tipos no seu código de aplicação para construir grandes cordas, tem de quebrar o código em pedaços e utilizar a declaração do EXEC.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a corda for curta, pode usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmicas continuarão sujeitas a todas as regras de validação da TSQL.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

