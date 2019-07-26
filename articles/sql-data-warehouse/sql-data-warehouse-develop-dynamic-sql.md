---
title: Usando SQL dinâmico no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para usar o SQL dinâmico no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4454b1d44d0be61dca8571e86c73e09a9527d1eb
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479664"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no SQL Data Warehouse
Dicas para usar o SQL dinâmico no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="dynamic-sql-example"></a>Exemplo de SQL dinâmico

Ao desenvolver o código do aplicativo para SQL Data Warehouse, talvez seja necessário usar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O SQL Data Warehouse não oferece suporte a tipos de dados de blob no momento. Não oferecer suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres, pois os tipos de dados blob incluem os tipos varchar (max) e nvarchar (max). Se você tiver usado esses tipos no código do aplicativo para criar cadeias de caracteres grandes, será necessário dividir o código em partes e usar a instrução EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalmente.

> [!NOTE]
> As instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação de TSQL.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

