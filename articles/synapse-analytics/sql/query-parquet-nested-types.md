---
title: Tipos aninhados de Parquet que usam sQL a pedido (pré-visualização)
description: Neste artigo, você vai aprender a consultar os tipos aninhados do Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744230"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Tipos aninhados de Parquet que usam SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on-demand (pré-visualização) em Azure Synapse Analytics.  Esta consulta vai ler os tipos aninhados de Parquet.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** com uma fonte de dados que se refira. Em seguida, inicialize os objetos executando o script de [configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="project-nested-or-repeated-data"></a>Dados aninhados ou repetidos do projeto

A seguinte consulta lê o ficheiro *justSimpleArray.parquet.* Projeta todas as colunas do ficheiro Parquet, incluindo dados aninhados ou repetidos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Elementos de acesso a partir de colunas aninhadas

A seguinte consulta lê o ficheiro *structExample.parquet* e mostra como superfície elementos de uma coluna aninhada:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elementos de acesso de colunas repetidas

A seguinte consulta lê o ficheiro *justSimpleArray.parquet* e utiliza [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **escalar** de dentro de uma coluna repetida, como um Array ou Map:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

A seguinte consulta lê o ficheiro *mapExample.parquet* e utiliza [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **não-escalar** dentro de uma coluna repetida, como um Array ou Map:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Passos seguintes

O próximo artigo irá mostrar-lhe como [consultar ficheiros JSON](query-json-files.md).
