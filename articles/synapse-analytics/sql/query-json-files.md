---
title: Consulta de ficheiros JSON utilizando sQL on-demand (pré-visualização)
description: Esta secção explica como ler ficheiros JSON utilizando a sql on-demand em Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431725"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consulta De ficheiros JSON utilizando SQL on-demand (pré-visualização) em Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL on-demand (pré-visualização) em Azure Synapse Analytics. O objetivo da consulta é ler ficheiros JSON.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, reveja os seguintes artigos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Amostra de ficheiros JSON

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

Para processar ficheiros JSON utilizando JSON_VALUE e [JSON_QUERY,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)tem de ler o ficheiro JSON a partir do armazenamento como uma única coluna. O seguinte guião lê o ficheiro *book1.json* como uma única coluna:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
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
> Está a ler todo o ficheiro JSON como uma única linha ou coluna. Assim, FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR estão definidos para 0x0b.

## <a name="query-json-files-using-json_value"></a>Consultar ficheiros JSON usando JSON_VALUE

A consulta abaixo mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalar (título, editor) de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por artigos selecionados:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

A seguinte consulta mostra como usar [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar objetos e matrizes (autores) de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por Tópicos Selecionados:*

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

## <a name="query-json-files-using-openjson"></a>Consultar ficheiros JSON usando OPENJSON

A seguinte consulta utiliza [O OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Irá recuperar objetos e propriedades dentro de um livro intitulado *Métodos Probabilísticos e Estatísticos em Criptologia, Uma Introdução por artigos selecionados:*

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Passos seguintes

Os próximos artigos desta série demonstrarão como:

- [Consultas de pastas e múltiplos ficheiros](query-folders-multiple-csv-files.md)
- [Criar e usar vistas.](create-use-views.md)
