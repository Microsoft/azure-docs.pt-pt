---
title: Consulta de ficheiros CSV utilizando sQL on-demand (pré-visualização)
description: Neste artigo, você aprenderá a consultar ficheiros CSV individuais com diferentes formatos de ficheiro usando sQL on-demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431595"
---
# <a name="query-csv-files"></a>Consultas de ficheiros CSV

Neste artigo, você aprenderá a consultar um único ficheiro CSV usando SQL on-demand (pré-visualização) em Azure Synapse Analytics. Os ficheiros CSV podem ter formatos diferentes: 

- Com e sem uma linha de cabeçalho
- Comma e valores delimitados por separadores
- Finais de linha de estilo Windows e Unix
- Valores não citados e citados, e personagens escapando

Todas as variações acima serão cobertas abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, reveja os seguintes artigos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Linha nova estilo Windows

A seguinte consulta mostra como ler um ficheiro CSV sem uma linha de cabeçalho, com uma nova linha ao estilo do Windows e colunas delimitadas com vírem.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho, nova linha estilo Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nova linha estilo Unix

A seguinte consulta mostra como ler um ficheiro sem uma linha de cabeçalho, com uma nova linha ao estilo Unix, e colunas delimitadas de vírem. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem linha de cabeçalho e com nova linha Unix-Style.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Linha do cabeçalho

A seguinte consulta mostra como um ficheiro de leitura com uma linha de cabeçalho, com uma nova linha estilo Unix, e colunas delimitadas de vírem. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha Unix-Style.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Personagem de citação personalizada

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha ao estilo Unix, colunas delimitadas de vírem e valores citados. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha unix-style e valores citados.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Esta consulta devolveria os mesmos resultados se omitisse o parâmetro FIELDQUOTE, uma vez que o valor padrão para FIELDQUOTE é uma cotação dupla.

## <a name="escaping-characters"></a>Personagens escapando

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha ao estilo Unix, colunas delimitadas de vírem, e um carvão de fuga usado para o delimitador de campo (vírina) dentro de valores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha unix-style e char de fuga usado para delimitador de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> Esta consulta falharia se o ESCAPECHAR não fosse especificado, uma vez que a vírposta em "Slov, enia" seria tratada como delimitador de campo em vez de parte do nome do país. "Slov, enia" seria tratado como duas colunas. Portanto, a linha em particular teria uma coluna mais do que as outras linhas, e uma coluna mais do que definiu na cláusula COM.

## <a name="tab-delimited-files"></a>Ficheiros deslimitados por separadores

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha ao estilo Unix, e colunas limitadas por separadores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha e delimitador de separadores unix-Style.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Subconjunto de colunas de retorno

Até agora, especificou o esquema de ficheiros CSV usando WITH e enumerando todas as colunas. Só pode especificar as colunas de que realmente precisa na sua consulta utilizando um número de ordinal para cada coluna necessária. Também omitirá colunas sem interesse.

A seguinte consulta devolve o número de nomes de países distintos num ficheiro, especificando apenas as colunas necessárias:

> [!NOTE]
> Veja a cláusula COM na consulta abaixo e note que há "2" (sem citações) no final da fila onde define a coluna *[country_name].* Significa que a coluna *[country_name]* é a segunda coluna do ficheiro. A consulta ignorará todas as colunas do ficheiro, exceto a segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Passos seguintes

Os próximos artigos mostrar-lhe-ão como:

- [Consulta de ficheiros Parquet](query-parquet-files.md)
- [Consultas de pastas e múltiplos ficheiros](query-folders-multiple-csv-files.md)
