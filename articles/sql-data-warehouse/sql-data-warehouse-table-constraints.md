---
title: Chaves primárias, estrangeiras e exclusivas
description: Suporte a restrições de tabela no Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 8f3102425c6f984df0f50bc05eeb6f9a5e66d3dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685488"
---
# <a name="primary-key-foreign-key-and-unique-key-in-azure-sql-data-warehouse"></a>Chave primária, chave estrangeira e chave exclusiva no Azure SQL Data Warehouse

Saiba mais sobre as restrições de tabela no SQL Data Warehouse do Azure, incluindo chave primária, chave estrangeira e chave exclusiva.

## <a name="table-constraints"></a>Restrições de tabela 
O SQL Data Warehouse do Azure dá suporte a essas restrições de tabela: 
- Só há suporte para a chave primária quando não CLUSTERIZAdo e não imposto são usados.    
- Só há suporte para a restrição UNIQUE quando não imposta é usado.   

Não há suporte para a restrição de chave estrangeira no Azure SQL Data Warehouse.  

## <a name="remarks"></a>Observações
Ter a chave primária e/ou a chave exclusiva permite que o mecanismo de data warehouse gere um plano de execução ideal para uma consulta.  Todos os valores em uma coluna de chave primária ou uma coluna de restrição UNIQUE devem ser exclusivos. 

Depois de criar uma tabela com uma restrição PRIMARY KEY ou Unique no Azure data warehouse, os usuários precisam garantir que todos os valores nessas colunas sejam exclusivos.  Uma violação disso pode fazer com que a consulta retorne um resultado impreciso.  Este exemplo mostra como uma consulta pode retornar um resultado impreciso se a coluna de restrição PRIMARY KEY ou Unique tiver valores duplicados.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Exemplos
Crie uma tabela de data warehouse com uma chave primária: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Criar uma tabela de data warehouse com uma restrição exclusiva:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar as tabelas para seu data warehouse, a próxima etapa é carregar dados na tabela. Para obter um tutorial de carregamento, consulte [carregando dados para SQL data warehouse](load-data-wideworldimportersdw.md).
