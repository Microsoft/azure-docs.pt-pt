---
title: Tipos de dados de tabela em Sinaapse SQL
description: Recomendações para a definição de tipos de dados de tabela no Sinaapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: cf627444407e7b0c43d15485fde3c342c6c24c7f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314877"
---
# <a name="table-data-types-in-synapse-sql"></a>Tipos de dados de tabela em Sinaapse SQL

Neste artigo, encontrará recomendações para definir tipos de dados de tabela no Sinaapse SQL. 

## <a name="data-types"></a>Tipos de dados

O Sinaapse SQL suporta os tipos de dados mais utilizados. Para obter uma lista dos tipos de dados suportados, consulte [os tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) na declaração CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar o comprimento da linha

Minimizar o tamanho dos tipos de dados encurta o comprimento da linha, o que leva a um melhor desempenho de consulta. Utilize o menor tipo de dados que funciona para os seus dados.

- Evite definir colunas de caracteres com um grande comprimento predefinido. Por exemplo, se o valor mais longo for de 25 caracteres, então defina a sua coluna como VARCHAR(25).
- Evite utilizar [NVARCHAR][NVARCHAR] quando só precisa de VARCHAR.
- Quando possível, utilize NVARCHAR(4000) ou VARCHAR(8000) em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

> [!NOTE]
> Se estiver a utilizar tabelas externas polyBase para carregar as suas tabelas Synapse SQL, o comprimento definido da linha de tabela não pode exceder 1 MB. Quando uma linha com dados de comprimento variável excede 1 MB, pode carregar a linha com o BCP, mas não com a PolyBase.

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
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarquia](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [imagem](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinário](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [texto](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Divida a coluna em várias colunas fortemente dactilografada. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Converta-se em tabelas temporárias ou considere armazenar dados para armazenamento utilizando [o CETAS](../sql/develop-tables-cetas.md). |
| [timetamp](/sql/t-sql/data-types/date-and-time-types) |Retraia o código para utilizar a [data 2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e a [função CURRENT_TIMESTAMP.](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Apenas as constantes são suportadas como incumprimentos, pelo current_timestamp não podem ser definidas como uma restrição de incumprimento. Se precisar de migrar os valores da versão da linha a partir de uma coluna de etiqueta de tempo, utilize [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) para valores de linha NÃO NULOS ou NULOS. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [tipo definido pelo utilizador](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta-se de volta para o tipo de dados nativo quando possível. |
| valores de incumprimento | Os valores predefinidos suportam apenas as constantes e literais. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de tabelas, consulte [a Visão Geral da Tabela](develop-overview.md).
