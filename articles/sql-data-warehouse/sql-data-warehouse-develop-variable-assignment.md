---
title: Atribuir variáveis no Azure SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para atribuição de variáveis de T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 62c4273a02e02aff268a96e1b13483088ba33f87
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861686"
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
