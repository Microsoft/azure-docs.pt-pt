---
title: Tipos de dados de tabela em Sinaapse SQL
description: Recomendações para a definição de tipos de dados de tabela no Sinaapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 907258dbce9b4f942601935df472e3515ed2d956
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020639"
---
# <a name="table-data-types-in-synapse-sql"></a>Tipos de dados de tabela em Sinaapse SQL

Recomendações para a definição de tipos de dados de tabela no Sinaapse SQL. 

## <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

O Sinaapse SQL suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte [os tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na declaração CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar o comprimento da linha

Minimizar o tamanho dos tipos de dados encurta o comprimento da linha, o que leva a um melhor desempenho de consulta. Utilize o menor tipo de dados que funciona para os seus dados.

- Evite definir colunas de caracteres com um grande comprimento predefinido. Por exemplo, se o valor mais longo for de 25 caracteres, então defina a sua coluna como VARCHAR(25).
- Evite utilizar [NVARCHAR][NVARCHAR] quando só precisa de VARCHAR.
- Quando possível, utilize NVARCHAR(4000) ou VARCHAR(8000) em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

> [!NOTE]
> Se estiver a utilizar as tabelas externas da PolyBase para carregar as suas mesas de bilhar SQL, o comprimento definido da linha de mesa não pode exceder 1 MB. Quando uma linha com dados de comprimento variável excede 1 MB, pode carregar a linha com o BCP, mas não com a PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados não suportados

Se estiver a migrar a sua base de dados de outra base de dados SQL, poderá encontrar tipos de dados que não sejam suportados no SQL da Synapse. Utilize esta consulta para descobrir tipos de dados não suportados no seu esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Soluções alternativas para tipos de dados não suportados

A lista a seguir mostra os tipos de dados que o Synapse SQL não suporta e oferece alternativas que pode utilizar em vez dos tipos de dados não suportados.

| Tipo de dados não suportado | Solução |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarquia](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [imagem](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Texto](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Divida a coluna em várias colunas fortemente dactilografada. |
| [tabela](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Se utilizar a piscina SQL, pode converter-se em mesas temporárias. Se utilizar o SQL (pré-visualização), poderá considerar armazenar dados para armazenamento utilizando [o CETAS](../sql/develop-tables-cetas.md). |
| [timetamp](/sql/t-sql/data-types/date-and-time-types) |Retraia o código para utilizar a [data 2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e a [função CURRENT_TIMESTAMP.](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Apenas as constantes são suportadas como incumprimentos, pelo current_timestamp não podem ser definidas como uma restrição de incumprimento. Se precisar de migrar os valores da versão da linha a partir de uma coluna de etiqueta de tempo, utilize [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) para valores de linha NÃO NULOS ou NULOS. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [tipo definido pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta-se de volta para o tipo de dados nativo quando possível. |
| valores de incumprimento | Os valores predefinidos suportam apenas as constantes e literais. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte [a Visão Geral da Tabela](develop-overview.md).
