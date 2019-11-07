---
title: Atribuir variáveis
description: Dicas para atribuir variáveis T-SQL no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1ae5285a8d1cf6fa391c082d0196b213e6b6a9c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692768"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Atribuindo variáveis no Azure SQL Data Warehouse

Dicas para atribuir variáveis T-SQL no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="setting-variables-with-declare"></a>Definindo variáveis com DECLARE

As variáveis no SQL Data Warehouse são definidas usando a instrução `DECLARE` ou a instrução `SET`. Inicializar variáveis com DECLARE é uma das maneiras mais flexíveis de definir um valor de variável em SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Você também pode usar DECLARE para definir mais de uma variável por vez. Você não pode usar SELECT ou UPDATE para fazer o seguinte:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Você não pode inicializar e usar uma variável na mesma instrução DECLARE. Para ilustrar o ponto, o exemplo a seguir **não** é permitido, pois @p1 é inicializado e usado na mesma instrução Declare. O exemplo a seguir apresenta um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definindo valores com SET

SET é um método comum para definir uma única variável.

As instruções a seguir são todas as maneiras válidas de definir uma variável com SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Você só pode definir uma variável por vez com SET. No entanto, os operadores compostos são permitidos.

## <a name="limitations"></a>Limitações

Você não pode usar UPDATE para atribuição de variável.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
