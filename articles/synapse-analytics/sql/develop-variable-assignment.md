---
title: Atribuir variáveis com Sinapse SQL
description: Neste artigo, encontrará dicas para atribuir variáveis T-SQL com Sinapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec59b7cc124a87b3939d095d03ee4a8bae9070f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685771"
---
# <a name="assign-variables-with-synapse-sql"></a>Atribuir variáveis com Sinapse SQL

Neste artigo, encontrará dicas para atribuir variáveis T-SQL com Sinapse SQL.

## <a name="set-variables-with-declare"></a>Definir variáveis com DECLARE

As variáveis em Synapse SQL são definidas usando a `DECLARE` declaração ou a `SET` declaração. Inicializar variáveis com DECLARE é uma das formas mais flexíveis de definir um valor variável em Synapse SQL.

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

Não é possível inicializar e utilizar uma variável na mesma declaração DE DECLARAÇÃO. Para ilustrar, o exemplo a seguir não é permitido, uma vez *\@ que o p1* é rubricado e usado na mesma declaração DE DECLARA. O exemplo a seguir dá um erro.

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

## <a name="next-steps"></a>Próximos passos

Para obter mais dicas de desenvolvimento, consulte o artigo de visão geral do [desenvolvimento do Synapse SQL.](develop-overview.md)
