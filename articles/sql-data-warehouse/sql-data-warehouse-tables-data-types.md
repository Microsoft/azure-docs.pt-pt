---
title: Definir tipos de dados - Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações para definir os tipos de dados de tabela no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06a273d3bfd5d416039a992e36bd4b0f72a85f78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65851553"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipos de dados de tabela no Azure SQL Data Warehouse
Recomendações para definir os tipos de dados de tabela no Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

SQL Data Warehouse suporta o mais usado tipos de dados. Para obter uma lista dos tipos de dados suportados, consulte [tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar o comprimento de linha
Minimizar o tamanho dos tipos de dados reduz o comprimento de linha, que leva a melhor desempenho de consulta. Utilize o tipo de dados mais pequeno que funciona para os seus dados. 

- Evite definir colunas de caracteres com um comprimento predefinido grande. Por exemplo, se o valor mais longo for 25 caracteres, em seguida, defina a coluna como VARCHAR(25). 
- Evite o uso de [NVARCHAR] [NVARCHAR] quando apenas precisa de VARCHAR.
- Sempre que possível, utilize NVARCHAR(4000) ou VARCHAR(8000) em vez de nvarchar (Max) ou varchar (Max).

Se estiver a utilizar tabelas externas do PolyBase para carregar as suas tabelas, o comprimento de definidos da linha da tabela não pode exceder 1 MB. Quando uma linha com dados de comprimento variável excede 1 MB, é possível carregar a linha com o BCP, mas não com o PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar os tipos de dados não suportado
Se estiver a migrar a base de dados de outra base de dados SQL, que poderá encontrar os tipos de dados que não são suportados no SQL Data Warehouse. Utilize esta consulta para detetar tipos de dados não suportado no seu esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Soluções alternativas para tipos de dados não suportado

A lista seguinte mostra os tipos de dados que o SQL Data Warehouse não suporta e oferece alternativas que podem ser utilizados em vez dos tipos de dados não suportado.

| Tipo de dados não suportado | Solução |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dividir coluna em várias colunas com rigidez de tipos. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Converta em tabelas temporárias. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Refazer o código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) e o [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) função. São suportadas constantes de apenas como padrões, portanto current_timestamp não pode ser definida como uma restrição default. Se precisar de migrar os valores de versão de linha de uma coluna com tipos de timestamp, em seguida, utilize [binário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para não nulo ou NULL valores da versão de linha. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [tipo definido pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta para o tipo de dados nativos sempre que possível. |
| valores predefinidos | Valores predefinidos suportam literais e constantes apenas. |


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o desenvolvimento de tabelas, veja [descrição geral da tabela](sql-data-warehouse-tables-overview.md).
