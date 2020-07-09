---
title: Consulta ficheiros JSON utilizando SQL on demand (pré-visualização)
description: Esta secção explica como ler ficheiros JSON usando SQL on demand in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5d02736e9cb0a612e434dc5a79a73d7a62785728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207656"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ficheiros de consulta JSON utilizando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on demand (preview) em Azure Synapse Analytics. O objetivo da consulta é ler ficheiros JSON. Os formatos suportados estão listados em [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="sample-json-files"></a>Amostra ficheiros JSON

A secção abaixo contém scripts de amostra para ler ficheiros JSON. Os ficheiros são armazenados num recipiente *json,* *livros*de pastas, e contêm uma única entrada de livro com a seguinte estrutura:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Ler ficheiros JSON

Para processar ficheiros JSON utilizando JSON_VALUE e [JSON_QUERY,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)é necessário ler o ficheiro JSON a partir do armazenamento como uma única coluna. O seguinte guião lê o *book1.jsficheiro* como uma única coluna:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Está a ler todo o ficheiro JSON como uma única linha ou coluna. Assim, o FIELDTERMINATOR, o FIELDQUOTE e o ROWTERMINATOR estão definidos para 0x0b.

## <a name="query-json-files-using-json_value"></a>Consultar ficheiros JSON usando JSON_VALUE

A consulta abaixo mostra-lhe como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalares (título, editor) de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por Tópicos Selecionados*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Consultar ficheiros JSON usando JSON_QUERY

A seguinte consulta mostra-lhe como usar [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar objetos e matrizes (autores) de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por Tópicos Selecionados*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Consulta ficheiros JSON usando OPENJSON

A seguinte consulta utiliza [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Recuperará objetos e propriedades dentro de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por Tópicos Selecionados*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Próximos passos

Os próximos artigos desta série demonstrarão como:

- [Consulta de pastas e vários ficheiros](query-folders-multiple-csv-files.md)
- [Criar e utilizar vistas](create-use-views.md)
