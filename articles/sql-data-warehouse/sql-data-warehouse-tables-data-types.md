---
title: Definindo tipos de dados-Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações para definir tipos de dados de tabela no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61bb977271186699b0a72389e1538573f978c56b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479376"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipos de dados de tabela no Azure SQL Data Warehouse
Recomendações para definir tipos de dados de tabela no Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

SQL Data Warehouse dá suporte aos tipos de dados usados com mais frequência. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar comprimento da linha
Minimizar o tamanho dos tipos de dados reduz o tamanho da linha, o que leva a um melhor desempenho de consulta. Use o menor tipo de dados que funciona para seus dados. 

- Evite definir colunas de caracteres com um comprimento padrão grande. Por exemplo, se o valor mais longo for 25 caracteres, defina a coluna como VARCHAR (25). 
- Evite usar [NVARCHAR] [NVARCHAR] quando precisar apenas de VARCHAR.
- Quando possível, use NVARCHAR (4000) ou VARCHAR (8000) em vez de NVARCHAR (MAX) ou VARCHAR (MAX).

Se você estiver usando tabelas externas do polybase para carregar as tabelas, o comprimento definido da linha da tabela não poderá exceder 1 MB. Quando uma linha com dados de comprimento variável exceder 1 MB, você poderá carregar a linha com BCP, mas não com o polybase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados sem suporte
Se você estiver migrando seu banco de dados de outro banco de dado SQL, você poderá encontrar tipos que não têm suporte no SQL Data Warehouse. Use esta consulta para descobrir tipos de dados sem suporte no esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Soluções alternativas para tipos de dados sem suporte

A lista a seguir mostra os tipos de dados aos quais SQL Data Warehouse não oferece suporte e fornece alternativas que você pode usar em vez dos tipos de dados sem suporte.

| Tipo de dados sem suporte | Solução |
| --- | --- |
| [Geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [gráfico](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Dividir a coluna em várias colunas fortemente tipadas. |
| [tabela](/sql/t-sql/data-types/table-transact-sql) |Converter em tabelas temporárias. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Retrabalho o código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) e a função [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) . Somente constantes são suportadas como padrões, portanto, CURRENT_TIMESTAMP não pode ser definido como uma restrição padrão. Se você precisar migrar valores de versão de linha de uma coluna tipada de carimbo de data/hora, use [Binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para não nulo ou valores de versão de linha NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [tipo definido pelo usuário](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta novamente para o tipo de dados nativo quando possível. |
| valores padrão | Os valores padrão dão suporte apenas a literais e constantes. |


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como desenvolver tabelas, consulte [visão geral da tabela](sql-data-warehouse-tables-overview.md).
