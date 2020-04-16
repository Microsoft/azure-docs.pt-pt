---
title: Atribuir variáveis com Synapse SQL
description: Neste artigo, encontrará dicas para atribuir variáveis T-SQL com Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428670"
---
# <a name="assigning-variables-with-synapse-sql"></a>Atribuindo variáveis com Synapse SQL

Neste artigo, encontrará dicas para atribuir variáveis T-SQL com Synapse SQL.

## <a name="setting-variables-with-declare"></a>Definição de variáveis com DECLAR

As variáveis em Synapse SQL são definidas utilizando a `DECLARE` declaração ou a `SET` declaração. Inicializar variáveis com DECLAR é uma das formas mais flexíveis de definir um valor variável em Synapse SQL.

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

Não pode inicializar e usar uma variável na mesma declaração de DECLARAÇÃO. Para ilustrar, o exemplo que *@p1* se segue não é permitido uma vez que é inicializado e usado na mesma declaração de DECLARAÇÃO. O exemplo que se segue dá um erro.

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

Não pode utilizar o UPDATE para a atribuição variável.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte o artigo de visão geral do [desenvolvimento synapse SQL.](develop-overview.md)