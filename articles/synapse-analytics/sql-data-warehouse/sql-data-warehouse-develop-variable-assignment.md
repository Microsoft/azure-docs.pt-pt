---
title: Atribuir variáveis
description: Neste artigo, você encontrará dicas essenciais para atribuir variáveis T-SQL em piscina SQL.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633411"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Atribuir variáveis em piscina Synapse SQL

Neste artigo, você encontrará dicas essenciais para atribuir variáveis T-SQL em piscina SQL.

## <a name="set-variables-with-declare"></a>Definir variáveis com DECLAR

As variáveis na piscina SQL são definidas utilizando a `DECLARE` declaração ou a `SET` declaração. Inicializar variáveis com DECLAR é uma das formas mais flexíveis de definir um valor variável no pool SQL.

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

Não pode inicializar e usar uma variável na mesma declaração de DECLARAÇÃO. Para ilustrar o ponto, **o** seguinte @p1 exemplo não é permitido, uma vez que é inicializado e utilizado na mesma declaração do DECLARE. Como tal, o exemplo seguinte dá um erro:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Definir valores com SET

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

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
