---
title: Usando rótulos para consultas de instrumento em SQL Data Warehouse | Microsoft Docs
description: Dicas para usar rótulos para consultas de instrumento no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ee991fdfcd93ea064d1205d61d07adf377cce667
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480024"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Usando rótulos para consultas de instrumento no Azure SQL Data Warehouse
Dicas para usar rótulos para consultas de instrumento no Azure SQL Data Warehouse para desenvolver soluções.


## <a name="what-are-labels"></a>O que são rótulos?
SQL Data Warehouse dá suporte a um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vejamos um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia de caracteres ' meu rótulo de consulta ' para a consulta. Essa marca é particularmente útil, pois o rótulo é habilitado para consulta por meio de DMVs. Consultar rótulos fornece um mecanismo para localizar consultas de problemas e ajudar a identificar o progresso por meio de uma execução de ELT.

Uma boa Convenção de nomenclatura realmente ajuda. Por exemplo, iniciar o rótulo com projeto, procedimento, instrução ou comentário ajuda a identificar exclusivamente a consulta entre todo o código no controle do código-fonte.

A consulta a seguir usa uma exibição de gerenciamento dinâmico para pesquisar por rótulo.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar colchetes ou aspas duplas ao contrário do rótulo da palavra ao consultar. O rótulo é uma palavra reservada e causa um erro quando não é delimitado. 
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).


