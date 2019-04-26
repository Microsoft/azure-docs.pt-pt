---
title: Com o SQL dinâmico no Azure SQL Data Warehouse | Documentos da Microsoft
description: Dicas para usar SQL dinâmico no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e1d209828313068e61b4acec3c4f92b7d643e1b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402518"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no SQL Data Warehouse
Dicas para usar SQL dinâmico no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="dynamic-sql-example"></a>Exemplo SQL dinâmico

Ao desenvolver o código da aplicação para o SQL Data Warehouse, poderá usar sql dinâmico para ajudar a fornecer soluções modulares, flexíveis e genéricas. O SQL Data Warehouse não suporta tipos de dados de blob neste momento. Não suportar os tipos de dados de BLOBs pode limitar o tamanho de cadeias de caracteres, uma vez que os tipos de dados de BLOBs incluem os tipos varchar (Max) e nvarchar (Max). Se tiver utilizado esses tipos no código da aplicação para criar cadeias de caracteres de grandes dimensões, terá de interromper o código em segmentos e utilizar a instrução EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, pode usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalmente.

> [!NOTE]
> Continuará a ser sujeitos a todas as regras de validação de TSQL executadas como SQL dinâmico de instruções.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).

