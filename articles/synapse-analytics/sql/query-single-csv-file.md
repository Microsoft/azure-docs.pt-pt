---
title: Ficheiros CSV de consulta utilizando SQL on demand (pré-visualização)
description: Neste artigo, você aprenderá a consultar ficheiros CSV únicos com diferentes formatos de ficheiro usando SQL on-demand (pré-visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 628631fb7fddbc07dcb865e3d3badbfb608ad097
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214456"
---
# <a name="query-csv-files"></a>Ficheiros CSV de consulta

Neste artigo, você aprenderá a consultar um único ficheiro CSV usando SQL on demand (pré-visualização) em Azure Synapse Analytics. Os ficheiros CSV podem ter formatos diferentes: 

- Com e sem uma linha de cabeçalho
- Valores de vírgula e delimitados
- Terminações de linha de estilo Windows e Unix
- Valores não citados e citados, e personagens escapando

Todas as variações acima serão cobertas abaixo.

## <a name="prerequisites"></a>Pré-requisitos

O seu primeiro passo é **criar uma base de dados** onde as tabelas serão criadas. Em seguida, inicialize os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são utilizados nestas amostras.

## <a name="windows-style-new-line"></a>Nova linha estilo Windows

A seguinte consulta mostra como ler um ficheiro CSV sem uma linha de cabeçalho, com uma nova linha estilo Windows e colunas delimitadas em vírgula.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho, nova linha estilo Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="unix-style-new-line"></a>Nova linha de estilo unix

A seguinte consulta mostra como ler um ficheiro sem uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas em vírgula. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV sem cabeçalho e com a nova linha Unix-Style.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="header-row"></a>Linha de cabeçalho

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas em vírgula. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com a nova linha Unix-Style.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula e valores citados. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha Unix-Style e valores citados.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha de estilo Unix, colunas delimitadas em vírgula, e um char de fuga usado para o delimiter de campo (vírgula) dentro de valores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha e char de escape Unix-Style usado para delimiter de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> Esta consulta falharia se o ESCAPECHAR não fosse especificado, uma vez que a vírgula em "Slov,enia" seria tratada como delimiter de campo em vez de parte do nome país/região. "Slov,enia" seria tratado como duas colunas. Portanto, a linha em particular teria uma coluna mais do que as outras linhas, e uma coluna mais do que você definiu na cláusula WITH.

### <a name="escaping-quoting-characters"></a>Escapando personagens citando

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula, e um carvão de citação dupla escapado dentro de valores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha unix, colunas delimitadas em vírgula, e um carvão de citação dupla escapado dentro de valores.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
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
    country_name = 'Slovenia';
```

> [!NOTE]
> O personagem citando deve ser escapado com outro personagem citando. Citar o carácter só pode aparecer dentro do valor da coluna se o valor for encapsulado com caracteres citantes.

## <a name="tab-delimited-files"></a>Ficheiros delimitados por separadores

A seguinte consulta mostra como ler um ficheiro com uma linha de cabeçalho, com uma nova linha estilo Unix e colunas delimitadas por separadores. Note a localização diferente do ficheiro em comparação com os outros exemplos.

Pré-visualização do ficheiro:

![Primeiras 10 linhas do ficheiro CSV com linha de cabeçalho e com nova linha e delimiter de separador Unix-Style.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

Até agora, especificou o esquema de ficheiros CSV usando WITH e listando todas as colunas. Só pode especificar as colunas de que necessita na sua consulta utilizando um número ordinal para cada coluna necessária. Também omitirá colunas sem interesse.

A seguinte consulta devolve o número de nomes distintos de país/região num ficheiro, especificando apenas as colunas necessárias:

> [!NOTE]
> Veja a cláusula COM na consulta abaixo e note que há "2" (sem cotações) no final da linha onde define a coluna *[country_name].* Significa que a coluna *[country_name]* é a segunda coluna do ficheiro. A consulta ignorará todas as colunas do ficheiro exceto a segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Próximos passos

Os próximos artigos irão mostrar-lhe como:

- [Consulta de ficheiros Parquet](query-parquet-files.md)
- [Consulta de pastas e vários ficheiros](query-folders-multiple-csv-files.md)
