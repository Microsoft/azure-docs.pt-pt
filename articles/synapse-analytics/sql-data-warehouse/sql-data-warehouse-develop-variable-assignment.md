---
title: Atribuir variáveis
description: Dicas para atribuir variáveis T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.
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
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351653"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Atribuindo variáveis no Armazém de Dados Azure SQL

Dicas para atribuir variáveis T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="setting-variables-with-declare"></a>Definição de variáveis com DECLAR

As variáveis no Armazém de Dados `DECLARE` SQL são definidas utilizando a declaração ou a `SET` declaração. Inicializar variáveis com DECLAR é uma das formas mais flexíveis de definir um valor variável no SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Também pode utilizar o DECLARE para definir mais do que uma variável de cada vez. Não é possível utilizar SELECT ou UPDATE para fazer o seguinte:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Não é possível inicializar e utilizar uma variável na mesma declaração de DECLARAÇÃO. Para ilustrar o ponto, **o** seguinte @p1 exemplo não é permitido, uma vez que é inicializado e utilizado na mesma declaração do DECLARE. O exemplo que se segue dá um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definição de valores com SET

O SET é um método comum para definir uma única variável.

As seguintes declarações são todas formas válidas de definir uma variável com SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Só é possível definir uma variável de cada vez com SET. No entanto, os operadores compostos são admissíveis.

## <a name="limitations"></a>Limitações

Não é possível utilizar o UPDATE para a atribuição variável.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
