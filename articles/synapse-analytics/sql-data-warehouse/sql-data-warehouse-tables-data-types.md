---
title: Definição de tipos de dados
description: Recomendações para a definição de tipos de dados de tabelas no Armazém de Dados Azure SQL.
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
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351319"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipos de dados de tabela sql no Armazém de Dados Azure SQL
Recomendações para a definição de tipos de dados de tabelas no Armazém de Dados Azure SQL. 

## <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

O SQL Data Warehouse suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte [os tipos](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) de dados na declaração create TABLE. 

## <a name="minimize-row-length"></a>Minimizar o comprimento da linha
Minimizar o tamanho dos tipos de dados encurta o comprimento da linha, o que leva a um melhor desempenho de consulta. Utilize o menor tipo de dados que funciona para os seus dados. 

- Evite definir colunas de caracteres com um grande comprimento predefinido. Por exemplo, se o valor mais longo for de 25 caracteres, então defina a sua coluna como VARCHAR(25). 
- Evite utilizar [NVARCHAR][NVARCHAR] quando só precisa de VARCHAR.
- Sempre que possível, utilize NVARCHAR (4000) ou VARCHAR (8000) em vez de NVARCHAR (MAX) ou VARCHAR (MAX).

Se estiver a utilizar tabelas externas polyBase para carregar as tabelas, o comprimento definido da linha de mesa não pode exceder 1 MB. Quando uma linha com dados de comprimento variável excede 1 MB, pode carregar a linha com o BCP, mas não com a PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados não suportados
Se estiver a migrar a sua base de dados de outra base de dados DaQL, poderá encontrar tipos de dados que não sejam suportados no SQL Data Warehouse. Utilize esta consulta para descobrir tipos de dados não suportados no seu esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Suposições para tipos de dados não suportados

A lista que se segue mostra os tipos de dados que o SQL Data Warehouse não suporta e oferece alternativas que pode utilizar em vez dos tipos de dados não suportados.

| Tipo de dados não suportados | Solução |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarquia](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [imagem](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [texto](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntexto](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Coluna dividida em várias colunas fortemente dactilografadas. |
| [tabela](/sql/t-sql/data-types/table-transact-sql) |Converta-se em mesas temporárias. |
| [carimbo de tempo](/sql/t-sql/data-types/date-and-time-types) |Reformular o código para utilizar a [data2](/sql/t-sql/data-types/datetime2-transact-sql) e a [função CURRENT_TIMESTAMP.](/sql/t-sql/functions/current-timestamp-transact-sql) Apenas as constantes são suportadas como incumprimentos, pelo que current_timestamp não pode ser definida como uma restrição padrão. Se precisar de migrar valores de versão de linha a partir de uma coluna dada por carimbo supor, utilize [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para valores de versão não nulo ou nulo. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [tipo definido pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta-se de volta ao tipo de dados nativos, quando possível. |
| valores predefinidos | Os valores predefinidos suportam apenas os literais e as constantes. |


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o desenvolvimento de tabelas, consulte a [visão geral da tabela](sql-data-warehouse-tables-overview.md).
