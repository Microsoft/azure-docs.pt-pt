---
title: Atribuir variáveis
description: Neste artigo, você encontrará dicas essenciais para a atribuição de variáveis T-SQL para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459229"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Atribuir variáveis para piscinas SQL dedicadas em Azure Synapse Analytics

Neste artigo, você encontrará dicas essenciais para atribuir variáveis T-SQL em piscina SQL dedicada.

## <a name="set-variables-with-declare"></a>Definir variáveis com DECLARE

As variáveis na piscina SQL dedicada são definidas usando a `DECLARE` declaração ou a `SET` declaração. Inicializar variáveis com DECLARE é uma das formas mais flexíveis de definir um valor variável na piscina SQL.

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

Não é possível inicializar e utilizar uma variável na mesma declaração DE DECLARAÇÃO. Para ilustrar o ponto, **não** é permitido o seguinte exemplo, uma vez @p1 que é rubricado e utilizado na mesma declaração DECLARA. Como tal, o exemplo a seguir dá um erro:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Definir valores com SET

SET é um método comum para definir uma única variável.

As seguintes declarações são todas formas válidas de definir uma variável com SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Só é possível definir uma variável de cada vez com o SET. No entanto, os operadores compostos são admissíveis.

## <a name="limitations"></a>Limitações

Não é possível utilizar o UPDATE para a atribuição variável.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
