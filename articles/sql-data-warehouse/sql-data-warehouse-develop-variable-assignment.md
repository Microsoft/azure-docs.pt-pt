---
title: Atribuir variáveis no Azure SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para atribuição de variáveis de T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ee97f7e5cda8b954fb697f73746e416d88d38c2d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401699"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Atribuição de variáveis no Azure SQL Data Warehouse

Sugestões para atribuição de variáveis de T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="setting-variables-with-declare"></a>Definir variáveis com DECLARE

Variáveis no SQL Data Warehouse são definidas utilizando o `DECLARE` instrução ou o `SET` instrução. A inicializar variáveis com DECLARE é uma das formas mais flexíveis para definir um valor da variável no SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Também pode utilizar DECLARE para definir mais de uma variável de cada vez. Não é possível utilizar SELECT ou ATUALIZAÇÃO para fazer o seguinte:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Não é possível inicializar e utilizar uma variável na mesma instrução DECLARE. Para ilustrar o ponto, o exemplo seguinte é **não** permitida uma vez que @p1 é inicializado tanto utilizado na mesma instrução DECLARE. O exemplo a seguir fornece um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Valores de definição de conjunto

CONJUNTO é um método comum para a definição de uma única variável.

As instruções seguintes são válidas maneiras de definir uma variável com o conjunto:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Apenas pode definir uma variável de cada vez com o conjunto. No entanto, os operadores compostas são permitidos.

## <a name="limitations"></a>Limitações

Não é possível utilizar a ATUALIZAÇÃO para atribuição de variáveis.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).
